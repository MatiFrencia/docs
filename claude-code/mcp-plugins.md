---
title: "MCP Plugins"
description: "Qué plugins instalar, qué API keys necesitás y cuántos tokens consume cada uno"
---

# MCP Plugins

Cada plugin habilitado carga sus tool schemas en el context window de cada sesión. El objetivo es mantenerse bajo ~25k tokens de overhead total.

---

## Plugins HIGH — Siempre activos

<AccordionGroup>
  <Accordion title="csharp-lsp — Diagnósticos C# en tiempo real">
    **Requiere:** VS Code con C# Dev Kit instalado

    Se conecta automáticamente al Language Server de VS Code. Aporta errores de compilación, intellisense y navegación de tipos dentro de las sesiones de Claude.
  </Accordion>

  <Accordion title="superpowers — Skills y workflows core">
    **Requiere:** Solo estar habilitado. Sin este plugin los demás workflows no funcionan correctamente.

    Activa: brainstorming, TDD, plans, debugging, code review.
  </Accordion>

  <Accordion title="github — Integración con GitHub">
    **Requiere:** `GITHUB_PERSONAL_ACCESS_TOKEN` en settings.json

    1. Ir a https://github.com/settings/tokens → Generate new token (classic)
    2. Scopes: `repo`, `read:org`, `workflow`
    3. Agregar al settings.json en `env`
  </Accordion>

  <Accordion title="context7 — Docs actualizadas de librerías">
    **Requiere:** Solo estar habilitado.

    Documentación actualizada de .NET, ASP.NET Core, EF Core, Npgsql. Usar antes de implementar con una librería nueva para evitar APIs deprecated.
  </Accordion>

  <Accordion title="microsoft-docs — Documentación oficial Microsoft">
    **Requiere:** Solo estar habilitado.

    Azure, .NET, M365, configuración, tutoriales oficiales.
  </Accordion>

  <Accordion title="commit-commands — Git workflow automatizado">
    Genera mensajes de commit descriptivos, sigue la convención del equipo.
    Habilita `/commit` y `/commit-push-pr`.
  </Accordion>
</AccordionGroup>

---

## Plugins MEDIUM — Según la tarea

| Plugin | API key | Qué aporta |
|--------|---------|-----------|
| `greptile` | `GREPTILE_API_KEY` (greptile.com) | Búsqueda semántica en el repo |
| `opsera-devsecops` | Auth de empresa | Security scanning, compliance |
| `coderabbit` | Cuenta CodeRabbit + GitHub | AI code review en PRs |
| `pr-review-toolkit` | Ninguna | Test coverage, silent failures, type design |
| `claude-md-management` | Ninguna | Auditar y mejorar CLAUDE.md |
| `code-simplifier` | Ninguna | Refactor de código recién escrito |

---

## Plugins LOW — Habilitar con `/plugin` cuando se necesiten

| Plugin | Tokens aprox. | Cuándo |
|--------|---------------|--------|
| `postman` | ~8,000 | Testing visual de APIs REST |
| `playwright` | ~3,000 | Tests E2E de browser |
| `chrome-devtools-mcp` | ~3,000 | Debugging de frontend |

---

## Impacto en tokens

```
csharp-lsp       ~800
superpowers      ~2,000
github           ~3,000
context7         ~500
microsoft-docs   ~500
commit-commands  ~800
feature-dev      ~1,500
code-review      ~1,500
pr-review-toolkit ~2,000
greptile         ~1,000
─────────────────────────
Total HIGH+MED   ~13,600 tokens
```

<Info>
Mantené el total de MCP overhead por debajo de ~25k tokens para evitar degradación del contexto disponible para código y specs.
</Info>

---

## Agregar un API key nuevo

1. Obtener el token del servicio
2. Editar `~/.claude/settings.json`:
   ```json
   { "env": { "NUEVA_API_KEY": "valor" } }
   ```
3. `/reload-plugins`

---

## Diagnóstico

```
/doctor          → errores de carga de plugins
/plugin          → ver plugins activos
/reload-plugins  → recargar después de cambiar settings.json
```
