---
title: "Referencia de plugins MCP"
description: "Qué plugin invocar para cada tipo de tarea — referencia rápida para agentes y developers"
---

# Referencia de plugins MCP

Usá esta tabla para decidir qué plugin invocar antes de ejecutar una tarea.

---

## HIGH — Uso diario, siempre disponibles

| Plugin | Cuándo usarlo |
|--------|---------------|
| `csharp-lsp` | Diagnósticos LSP, errores de compilación, navegación de tipos C# |
| `superpowers` | Activa todos los workflows core. Base de todo — invocar primero |
| `github` | PRs, issues, branches, reviews — todo lo relacionado a GitHub |
| `context7` | Docs actualizadas de .NET, ASP.NET Core, EF Core — antes de usar una API nueva |
| `microsoft-docs` | Docs de Azure, .NET, M365 — cuando `context7` no alcance |
| `commit-commands` | Commit + push + PR en un flujo automatizado |
| `feature-dev` | Análisis del codebase + arquitectura de la feature antes de implementar |
| `code-review` | Code reviews estructuradas con agentes especializados |

---

## MEDIUM — Usar según la tarea

| Plugin | Cuándo usarlo |
|--------|---------------|
| `pr-review-toolkit` | Toolkit completo de PR: test coverage, silent failures, type design |
| `opsera-devsecops` | Security scanning, análisis de arquitectura, compliance, SQL injection |
| `coderabbit` | AI code review en GitHub, auto-fix de comentarios de review |
| `claude-md-management` | Auditar y mejorar CLAUDE.md al cierre de sesiones importantes |
| `code-simplifier` | Refactor y limpieza de código recién escrito |
| `greptile` | Búsqueda semántica profunda en el repo, contexto de PRs pasados |

---

## LOW — Activar con `/plugin` cuando sea necesario

| Plugin | Cuándo usarlo |
|--------|---------------|
| `postman` | Testing visual de APIs REST, generar colecciones |
| `playwright` | Tests E2E de browser, automatización web |
| `chrome-devtools-mcp` | Debugging de frontend en Chrome |
| `skill-creator` | Crear o mejorar skills de Claude Code |
| `plugin-dev` | Desarrollar plugins, hooks, agents, commands |

---

## Regla general para agentes

<Steps>
  <Step title="Verificar plugin HIGH">
    Antes de cualquier tarea, verificar si aplica algún plugin HIGH e invocarlo primero.
  </Step>
  <Step title="Complementar con MEDIUM">
    Si la tarea requiere security scan, code review o búsqueda semántica, agregar el plugin MEDIUM correspondiente.
  </Step>
  <Step title="LOW solo si es explícito">
    No cargar plugins LOW a menos que la tarea lo justifique explícitamente.
  </Step>
  <Step title="Ante duda sobre una API">
    Ante cualquier duda sobre una API de .NET/Azure → `context7` o `microsoft-docs` antes de asumir.
  </Step>
</Steps>
