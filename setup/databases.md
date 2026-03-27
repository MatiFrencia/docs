---
title: "Bases de datos y Docker"
description: "PostgreSQL y MongoDB via docker-compose, GUI clients y Testcontainers"
---

# Bases de datos y Docker

El stack usa **PostgreSQL** como base de datos relacional y **MongoDB** para datos no relacionales. Ambas corren via Docker para garantizar portabilidad entre entornos.

---

## Estrategia por entorno

| Entorno | Cómo corre |
|---------|------------|
| Desarrollo local | `docker compose up` — contenedores persistentes |
| Integration tests | Testcontainers — contenedores efímeros por test run |
| CI/CD | Testcontainers en el runner |
| Producción | Instancia gestionada (RDS, Atlas, etc.) |

---

## docker-compose base

```yaml
# docker-compose.yml
services:
  postgres:
    image: postgres:16-alpine
    container_name: app_postgres
    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: apppass
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U appuser -d appdb"]
      interval: 10s
      timeout: 5s
      retries: 5

  mongodb:
    image: mongo:7
    container_name: app_mongo
    environment:
      MONGO_INITDB_ROOT_USERNAME: appuser
      MONGO_INITDB_ROOT_PASSWORD: apppass
      MONGO_INITDB_DATABASE: appdb
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

  pgadmin:
    image: dpage/pgadmin4:latest
    container_name: app_pgadmin
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@local.dev
      PGADMIN_DEFAULT_PASSWORD: localonly
    ports:
      - "5050:80"
    depends_on:
      postgres:
        condition: service_healthy

volumes:
  postgres_data:
  mongo_data:
```

<Warning>
Las credenciales del docker-compose son solo para desarrollo local. Nunca usar en producción ni commitear credenciales reales.
</Warning>

### Comandos esenciales

```bash
docker compose up -d          # levantar en background
docker compose ps             # ver estado
docker compose logs -f postgres  # ver logs en tiempo real
docker compose stop           # detener sin borrar datos
docker compose down           # detener y borrar contenedores
docker compose down -v        # reset completo (borra volumes)
```

---

## Connection strings

### PostgreSQL

```
Host=localhost;Port=5432;Database=appdb;Username=appuser;Password=apppass
```

### MongoDB

```
mongodb://appuser:apppass@localhost:27017/appdb?authSource=admin
```

---

## EF Core con PostgreSQL (Npgsql)

```bash
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL
dotnet add package Microsoft.EntityFrameworkCore.Design
```

Registro en `Program.cs`:

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseNpgsql(
        builder.Configuration.GetConnectionString("Database")
        ?? Environment.GetEnvironmentVariable("DATABASE_URL")
    )
);
```

---

## GUI Clients

### PostgreSQL — DBeaver (gratuito)
Descargá desde https://dbeaver.io/download/ → Nueva conexión → PostgreSQL → Test Connection

**Alternativa:** pgAdmin en `http://localhost:5050` (ya incluido en el compose)

### MongoDB — Compass (gratuito)
Descargá desde https://www.mongodb.com/try/download/compass
URI: `mongodb://appuser:apppass@localhost:27017/?authSource=admin`

---

## Testcontainers para integration tests

No requiere configuración adicional — solo Docker corriendo.

```bash
dotnet add package Testcontainers.PostgreSql
dotnet add package Microsoft.AspNetCore.Mvc.Testing
```

Patrón base:

```csharp
public class IntegrationTestBase : IAsyncLifetime
{
    private readonly PostgreSqlContainer _postgres = new PostgreSqlBuilder()
        .WithImage("postgres:16-alpine")
        .WithDatabase("testdb")
        .WithUsername("testuser")
        .WithPassword("testpass")
        .Build();

    public async Task InitializeAsync() => await _postgres.StartAsync();
    public async Task DisposeAsync() => await _postgres.DisposeAsync();
}
```

<Info>
Cada clase de test crea su propio contenedor efímero. Más lento que mocks, pero garantiza aislamiento total y tests que reflejan el comportamiento real en producción.
</Info>
