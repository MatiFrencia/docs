---
title: "Convenciones del proyecto"
description: "Arquitectura en capas, naming, EF Core, tests, logging y AutoMapper para C# backend"
---

# Convenciones del proyecto

---

## Arquitectura en capas

```
Project.ApiService       ← Host: controllers, Program.cs, Swagger. Sin lógica de negocio.
Project.Domain           ← Business logic, entities, interfaces, validators, JWT
Project.Infrastructure   ← EF Core context, repositories, migrations, servicios externos
Project.IntegrationTests ← xUnit + WebApplicationFactory + Testcontainers
```

**Regla de dependencias:**
```
ApiService → Domain ← Infrastructure
ApiService → Infrastructure (solo para DI en Program.cs)
```

- `Domain` no referencia nada de `Infrastructure`
- La lógica de negocio vive en `Domain`, nunca en controllers ni repositorios
- Los controllers son delgados: validan, llaman al servicio, devuelven la respuesta

---

## Pipeline de un request

```
Request
  → CorrelationIdMiddleware     (agrega X-Correlation-Id)
  → ExceptionHandlingMiddleware (ProblemDetails en excepciones)
  → Serilog                    (logs con correlation ID)
  → CORS → JWT Auth → Authorization
  → Controller → Service (Domain) → Repository (Infrastructure) → DB
```

---

## Naming conventions

| Elemento | Convención | Ejemplo |
|----------|------------|---------|
| Clases, interfaces | PascalCase | `PatientService`, `IPatientRepository` |
| Métodos | PascalCase | `GetPatientByIdAsync` |
| Propiedades | PascalCase | `FirstName` |
| Campos privados | `_camelCase` | `_dbContext` |
| Variables locales | camelCase | `patientId` |
| Interfaces | Prefijo `I` | `IPatientRepository` |

### Sufijos por tipo de clase

| Sufijo | Qué es |
|--------|--------|
| `Controller` | ASP.NET Core controller |
| `Service` | Lógica de negocio orquestadora |
| `Repository` | Acceso a datos |
| `Request` | DTO de entrada (body de POST/PUT) |
| `Response` | DTO de salida |
| `Validator` | FluentValidation validator |
| `Profile` | AutoMapper profile |
| `Middleware` | Middleware de ASP.NET Core |

---

## EF Core — Flujo de migraciones

```bash
# Crear migración
dotnet ef migrations add NombreDescriptivo \
  --project Project.Infrastructure \
  --startup-project Project.ApiService

# Convenciones de nombres
AddPatientsTable
AddPatientEmailIndex
AlterAppointmentAddCancelledAtColumn

# Aplicar
dotnet ef database update \
  --project Project.Infrastructure \
  --startup-project Project.ApiService
```

- Usar `AsNoTracking()` por defecto en queries de lectura
- Proyectar solo las columnas necesarias — no `Select *`
- No encadenar `Include()` sin paginación

---

## Integration Tests

```csharp
public class AppointmentTests : IntegrationTestBase
{
    [Fact]
    public async Task GetAppointments_WithValidToken_ReturnsOk()
    {
        // Arrange
        var token = await GetAuthTokenAsync("user@test.com", "password");
        _client.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", token);

        // Act
        var response = await _client.GetAsync("/api/appointments");

        // Assert
        response.EnsureSuccessStatusCode();
    }
}
```

**Reglas:**
- No mockear la base de datos — usar Testcontainers (PostgreSQL real)
- Un test = una assertion lógica
- No reutilizar datos entre tests — cada test siembra los propios
- Nombre: `Metodo_Condicion_ResultadoEsperado`

```bash
dotnet test Project.IntegrationTests
dotnet test --filter "ClassName=AppointmentTests"
dotnet test --verbosity detailed
```

---

## Logging con Serilog — structured logging

```csharp
// MAL
_logger.LogInformation($"Paciente {patientId} actualizado");

// BIEN
_logger.LogInformation("Paciente {PatientId} actualizado", patientId);
```

El Correlation ID se agrega automáticamente via `CorrelationIdMiddleware`. Todos los logs de un request comparten el mismo `CorrelationId`.

---

## FluentValidation

```csharp
public class CreatePatientRequestValidator : AbstractValidator<CreatePatientRequest>
{
    public CreatePatientRequestValidator()
    {
        RuleFor(x => x.Email)
            .NotEmpty().EmailAddress().MaximumLength(255);
        RuleFor(x => x.FirstName)
            .NotEmpty().MaximumLength(100);
    }
}
```

---

## AutoMapper

```csharp
public class MappingProfile : Profile
{
    public MappingProfile()
    {
        CreateMap<Patient, PatientResponse>();
        CreateMap<CreatePatientRequest, Patient>();
    }
}
```
