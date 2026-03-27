---
title: "Estructura Greenfield"
description: "Árbol completo de archivos para un proyecto SDD nuevo: 1 feature cerrada, 1 WIP y todos los opcionales"
---

# Estructura de archivos — Proyecto Greenfield SDD

Árbol completo con todos los archivos que deben existir en un proyecto nuevo usando SDD, con 1 feature cerrada y 1 en WIP.

---

## Árbol completo

```
PROJECT_ROOT/
│
├── CLAUDE.md                          [MANDATORIO] ★
├── README.md                          [MANDATORIO]
├── CHANGELOG.md                       [RECOMENDADO]
├── .editorconfig                      [RECOMENDADO]
├── .gitignore                         [MANDATORIO]
├── .env.example                       [MANDATORIO]
├── .env                               [MANDATORIO, en .gitignore]
│
├── .claude/
│   ├── settings.json                  [MANDATORIO, commitear]
│   ├── settings.local.json            [OPCIONAL, en .gitignore]
│   ├── mcp-guide.md                   [RECOMENDADO]
│   └── getting-started/               [RECOMENDADO]
│       └── README.md + secciones
│
├── specs/
│   ├── _template.md                   [RECOMENDADO]
│   ├── closed/
│   │   └── 001-nombre-feature.md      ← feature completada
│   └── wip/
│       └── 002-nombre-feature.md      ← feature en desarrollo
│
├── docs/
│   ├── architecture.md                [RECOMENDADO]
│   ├── adr/
│   │   ├── README.md
│   │   ├── 001-decision-tecnologia.md
│   │   └── 002-arquitectura-capas.md
│   └── runbooks/
│       ├── migrations.md
│       └── deployment.md
│
├── .github/
│   ├── workflows/
│   │   └── ci.yml                     [RECOMENDADO]
│   └── pull_request_template.md       [RECOMENDADO]
│
└── docker-compose.yml                 [MANDATORIO para este stack]
```

---

## Leyenda

| Tag | Significado |
|-----|-------------|
| **MANDATORIO** | Sin esto el proyecto no funciona correctamente |
| **RECOMENDADO** | Alta diferencia en productividad — implementar en la primera semana |
| **OPCIONAL** | Útil según el contexto del equipo |

---

## specs/_template.md

```markdown
# Feature: [Nombre descriptivo]

**ID:** [número]
**Status:** Draft | In Review | Approved | In Progress | Done
**Creada:** YYYY-MM-DD
**Cerrada:** YYYY-MM-DD
**PR:** #[número]

---

## Problema
¿Qué problema resuelve? ¿A quién afecta?

## Objetivos
- Objetivo 1

## Fuera de scope
- Cosa que NO hacemos en esta iteración

---

## Diseño técnico

### Cambios en la API
| Método | Endpoint | Descripción |
|--------|----------|-------------|

### Cambios en la base de datos
[migraciones necesarias]

### Lógica de negocio
[reglas, validaciones, flujos]

---

## Plan de tests
- [ ] Test: [escenario]

## Acceptance Criteria
- [ ] AC1: Dado [contexto], cuando [acción], entonces [resultado]

## Notas de implementación
[decisiones tomadas, problemas encontrados]
```

---

## Ejemplo spec cerrada (001)

```markdown
# Feature: Login con Google OAuth

**Status:** Done ✓ | **PR:** #7 | **Cerrada:** 2026-01-15

## Problema
Los usuarios abandonaban el registro por email/password.
Necesitamos reducir la fricción de onboarding.

## Diseño técnico
- POST /api/identity/google-login
- Columna GoogleId en Users + migración AddGoogleIdToUsers
- Valida token con Google.Apis.Auth, crea usuario si no existe, devuelve JWT

## Acceptance Criteria
- [x] Token válido → 200 + JWT con claims correctos
- [x] Primera vez → usuario creado en BD
- [x] Token inválido → 401
```

---

## Ejemplo spec WIP (002)

```markdown
# Feature: Historial de turnos por paciente

**Status:** In Progress | **PR:** — (pendiente)

## Problema
No hay forma de ver el historial de un paciente desde la app.

## Diseño técnico
- GET /api/pacientes/{id}/turnos con paginación y filtros
- Sin migraciones — usa tablas existentes

## Acceptance Criteria
- [x] Lista paginada devuelta correctamente
- [x] Dentista sin acceso → 403
- [ ] Filtros de fecha y estado funcionan
- [ ] Respuesta incluye nombre del dentista
```

---

## .github/pull_request_template.md

```markdown
## Descripción
[Qué hace este PR en una o dos oraciones]

## Spec relacionada
- Spec: `specs/wip/[archivo].md`
- Issue: #[número]

## Tipo de cambio
- [ ] Feature nueva
- [ ] Bug fix
- [ ] Refactor
- [ ] Migración de base de datos

## Checklist
- [ ] Tests pasan localmente
- [ ] Agregué tests para el nuevo comportamiento
- [ ] CLAUDE.md actualizado si cambió la arquitectura
- [ ] Spec movida a `specs/closed/` si la feature está completa
- [ ] No hay secrets en el código
```

---

## Cuándo crear qué

| Momento | Archivos a crear |
|---------|-----------------|
| Primer commit | `CLAUDE.md`, `README.md`, `.gitignore`, `.env.example`, `.editorconfig`, `.claude/settings.json`, `docker-compose.yml` |
| Antes de primera feature | `specs/_template.md`, `specs/wip/001-nombre.md`, primer ADR |
| Al cerrar una feature | Mover spec a `specs/closed/`, actualizar `CHANGELOG.md` |
| Primera semana | `.claude/mcp-guide.md`, `docs/architecture.md` |
| Primer mes | Todos los ADRs, `docs/runbooks/`, CI workflow, PR template |
