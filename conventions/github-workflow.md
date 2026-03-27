---
title: "GitHub Workflow"
description: "Nombres de rama, PRs, GitHub Projects, milestones, merge commit y branch protection"
---

# GitHub Workflow

---

## Flujo completo

```
Jira ticket
    ↓
Crear rama desde main
    ↓
Desarrollar (commits siguiendo la convención)
    ↓
Abrir PR → linkear a GitHub Project y Milestone
    ↓
CI pasa + 1 approval
    ↓
Merge commit a main
    ↓
Borrar rama + cerrar ticket + mover spec a closed/
```

---

## Nombres de rama

### Formato

```
<type>/<JIRA-TICKET>-<descripcion-en-kebab-case>
```

### Ejemplos

```bash
feature/PROJ-123-batch-renotification
fix/PROJ-456-request-racing
enhancement/PROJ-789-paginate-patient-history
hotfix/PROJ-321-null-ref-on-empty-barcode
refactor/PROJ-654-extract-jwt-service
chore/PROJ-111-update-nuget-dependencies
test/PROJ-222-integration-tests-auth
docs/PROJ-333-update-conventions
```

### Sin ticket de Jira

```bash
feature/no-ticket-descripcion-corta
docs/no-ticket-getting-started-sdd
```

### Reglas
- Todo en **minúsculas**, palabras con **guión**
- El ticket va entre el tipo y la descripción
- Sin barra al final

### Crear la rama

```bash
git checkout main && git pull origin main
git checkout -b feature/PROJ-123-descripcion
```

---

## Pull Request

### Título

Igual que el header del commit — se convierte en el mensaje del merge commit:

```
feature: add batch renotification endpoint for package sorter
fix: prevent racing of concurrent requests
```

### Descripción

Replica la estructura del body del commit (párrafo de apertura + secciones + `## Files` + `resolve:`).

### Draft PR

Para feedback temprano o CI sin estar listo:
```
[WIP] feature: add batch renotification endpoint
```

---

## GitHub Projects y Milestones

### GitHub Projects

Al abrir el PR → sidebar → **Projects** → seleccionar el proyecto activo.

Mover la card según el estado:
- `In Progress` — durante el desarrollo
- `In Review` — al abrir el PR final
- `Done` — al mergear

### Milestones

Cada PR debe tener un milestone asignado:
- Por versión: `v1.2.0`
- Por sprint: `Sprint 14 — 2026-04-07`

Crear milestones en GitHub → Issues → Milestones → New milestone.

```
Milestone "v1.2.0"
  ├── feature/PROJ-123 — batch renotification   [merged]
  ├── fix/PROJ-456    — request racing          [merged]
  └── feature/PROJ-789 — pagination             [in review]
```

---

## Branch protection en main

Settings → Branches → Add rule para `main`:

| Regla | Valor |
|-------|-------|
| Require a pull request before merging | ✓ |
| Required approvals | **1** |
| Dismiss stale approvals when new commits pushed | ✓ |
| Require status checks to pass | ✓ |
| Status checks requeridos | CI (build + tests) |
| Require branches to be up to date | ✓ |
| Do not allow bypassing | ✓ |
| Allow force pushes | ✗ |
| Allow deletions | ✗ |

---

## CI mínimo

```yaml
# .github/workflows/ci.yml
name: CI
on:
  pull_request:
    branches: [main]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '8.x'
      - run: dotnet build MiProyecto.sln --configuration Release
      - run: dotnet test MiProyecto.IntegrationTests
```

<Info>
Testcontainers levanta PostgreSQL automáticamente en el runner de GitHub Actions — `ubuntu-latest` incluye Docker.
</Info>

---

## Estrategia de merge: Merge Commit

Siempre **"Create a merge commit"** — nunca Squash, nunca Rebase.

**Por qué:** preserva el historial completo del branch. El título del PR = mensaje del merge commit = sigue la convención. `git log main` queda limpio.

Settings → General → Pull Requests:
- ✓ Allow merge commits
- ✗ Allow squash merging
- ✗ Allow rebase merging
- ✓ Automatically delete head branches

---

## Después del merge

```bash
# Si la rama no se borró automáticamente
git push origin --delete feature/PROJ-123-descripcion
git checkout main && git pull origin main
git branch -d feature/PROJ-123-descripcion
```

- Mover card en GitHub Projects a `Done`
- Cerrar ticket en Jira
- Mover spec: `specs/wip/ → specs/closed/` completando `Cerrada:` y `PR:`

---

## Mantener la rama actualizada

```bash
# Merge de main al branch (recomendado — consistente con merge commit)
git checkout feature/PROJ-123-descripcion
git fetch origin
git merge origin/main
```

---

## Referencia rápida

```
Nueva rama:   git checkout main && git pull && git checkout -b <type>/PROJ-XXX-<desc>
Abrir PR:     Título = "<type>: <desc>" / Project + Milestone
Merge:        Merge commit (nunca squash/rebase)
Post-merge:   Borrar rama + spec a closed/ + cerrar Jira
```
