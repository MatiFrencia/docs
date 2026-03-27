---
title: "Herramientas del sistema"
description: "Git, .NET 8 SDK, Node.js, Docker Desktop y terminal"
---

# Herramientas del sistema

Instalá estas herramientas en orden. Todas son requisito previo para el resto del setup.

---

## 1. Git

Descargá desde https://git-scm.com/download/win e instalá con las opciones por defecto.

```bash
git --version
# git version 2.x.x
```

### Configuración global mínima

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@empresa.com"
git config --global core.autocrlf true
git config --global core.editor "code --wait"
git config --global init.defaultBranch main
git config --global pull.rebase false
git config --global push.default current
git config --global credential.helper manager
```

### Aliases útiles

```bash
git config --global alias.st "status -sb"
git config --global alias.lg "log --oneline --graph --decorate --all"
git config --global alias.undo "reset HEAD~1 --mixed"
```

---

## 2. .NET 8 SDK

Descargá el **SDK** (no Runtime) desde https://dotnet.microsoft.com/download/dotnet/8.0

```bash
dotnet --version   # 8.x.x
dotnet --list-sdks # debe aparecer 8.x.x
```

### EF Core CLI

```bash
dotnet tool install --global dotnet-ef
dotnet ef --version   # Entity Framework Core .NET Command-line Tools 8.x.x
```

---

## 3. Node.js LTS

Claude Code requiere Node.js. Recomendado usar **nvm-windows**:
1. Descargá desde https://github.com/coreybutler/nvm-windows/releases
2. Instalá y ejecutá:

```bash
nvm install lts
nvm use lts

node --version   # v20.x.x o superior
npm --version    # 10.x.x o superior
```

---

## 4. Docker Desktop

Requerido para levantar bases de datos localmente y para Testcontainers en los integration tests.

1. Descargá desde https://www.docker.com/products/docker-desktop/
2. Instalá y reiniciá el equipo
3. Settings → General → "Start Docker Desktop when you log in" ✓

```bash
docker --version         # Docker version 24.x.x
docker compose version   # Docker Compose version v2.x.x
docker run hello-world   # debe imprimir "Hello from Docker!"
```

<Warning>
Docker Desktop debe estar corriendo siempre que trabajés. Los integration tests fallan si Docker no está activo.
</Warning>

---

## 5. PowerShell 7 (recomendado)

```bash
winget install Microsoft.PowerShell
pwsh --version   # PowerShell 7.x.x
```

---

## Checklist final

```bash
git --version         # ✓ 2.x.x
dotnet --version      # ✓ 8.x.x
dotnet ef --version   # ✓ 8.x.x
node --version        # ✓ v20.x.x
npm --version         # ✓ 10.x.x
docker --version      # ✓ 24.x.x
docker compose version # ✓ v2.x.x
```
