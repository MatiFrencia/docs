---
title: "Setup Brownfield"
description: "Cómo hacer AI-friendly y SDD-ready un proyecto existente que no usa SDD"
---

# Setup Brownfield

Guía para tomar un proyecto en producción que no usa SDD y convertirlo en un proyecto donde Claude Code pueda trabajar con confianza.

---

## El problema

Un proyecto existente tiene **conocimiento implícito**: decisiones que nadie documentó, convenciones que se transmiten de boca en boca, código que hace cosas raras por razones históricas. Claude Code sin contexto viola ese conocimiento constantemente.

**El objetivo no es reescribir el proyecto, sino capturar ese conocimiento en archivos que Claude pueda leer.**

---

## Fase 0: Auditoría inicial

Antes de crear cualquier archivo:

```
Analizá este repositorio y describí:
1. La arquitectura general y las capas del sistema
2. Los patrones de código repetidos
3. Las convenciones de naming que identifiques
4. Qué partes parecen más complejas o con más deuda técnica
5. Qué comandos existen para correr y testear
```

Usá el output como base para el CLAUDE.md.

---

## Fase 1: Crear el CLAUDE.md

```markdown
# CLAUDE.md

## Índice de documentación
| Documento | Qué encontrás |
|-----------|--------------|
| [`specs/wip/`](./specs/wip/) | Features en desarrollo |
| [`.claude/mcp-guide.md`](./.claude/mcp-guide.md) | Guía de plugins MCP |

## Comandos
\`\`\`bash
# Build, run, test, migrations...
\`\`\`

## Arquitectura
[descripción de capas]

## Convenciones
[naming, patrones, qué se hace y qué no]

## Zonas de riesgo
[partes que requieren cuidado especial]

## Variables de entorno
[tabla de variables]

## Deuda técnica conocida
[lista de problemas que NO hay que "arreglar" sin consultar]
```

---

## Fase 2: Estructura .claude/

```bash
mkdir -p .claude
```

`.claude/settings.json` (commitear):
```json
{
  "permissions": {
    "allow": ["Bash(dotnet:*)", "Bash(docker:*)", "Bash(git log:*)", "Bash(git status:*)"],
    "deny": ["Bash(git push --force:*)", "Bash(dotnet ef database drop:*)"]
  }
}
```

Agregar al `.gitignore`:
```
.claude/settings.local.json
```

---

## Fase 3: ADRs para decisiones existentes

Para cada decisión arquitectónica importante, crear un ADR en `docs/adr/`:

```markdown
# ADR-001: [Título]

**Fecha:** YYYY-MM-DD | **Estado:** Accepted

## Contexto
¿Qué llevó a esta decisión?

## Decisión
¿Qué se decidió?

## Consecuencias
Trade-offs positivos y negativos.
```

Empezar con las 3-5 decisiones más críticas.

---

## Fase 4: Indexar con Greptile

Con el plugin `greptile` habilitado, la primera vez que lo usás indexa el repo automáticamente (5-10 minutos en repos grandes).

Verificar:
```
Usando Greptile, buscá dónde se maneja la autenticación en este proyecto.
```

---

## Fase 5: Auditar secrets hardcodeados

```
Hacé un security scan del repo y detectá secrets hardcodeados.
```

Mover cualquier secret encontrado a variables de entorno.

---

## Fase 6: Documentar tests existentes

- Si hay tests: documentar cómo correrlos, qué cubren, cuáles son flaky
- Si no hay tests: crear plan de adopción gradual en CLAUDE.md:

```markdown
## Tests

Actualmente sin cobertura. Plan de adopción:
- [ ] Tests de smoke para endpoints principales
- [ ] Tests del flujo de autenticación
- [ ] Tests del módulo más crítico
```

---

## Regla de convivencia SDD en brownfield

> **Código viejo:** se mantiene tal cual, se refactoriza solo cuando hay una razón concreta.
>
> **Código nuevo:** siempre sigue SDD — spec → TDD → implementación → review.

Para la primera feature nueva en un brownfield:

```
Quiero agregar [feature X]. Antes de arrancar:
1. Explorá el código relevante para esta feature
2. Identificá qué partes vamos a tocar y cuáles no
3. Hagamos brainstorming respetando los patrones existentes
```

---

## Checklist brownfield

### Día 1 (mínimo viable)
- [ ] `CLAUDE.md` con índice, comandos, arquitectura y convenciones
- [ ] `.claude/settings.json` con permisos básicos
- [ ] `.gitignore` actualizado (`.env`, `settings.local.json`)
- [ ] `env.example` creado

### Primera semana
- [ ] Greptile indexando el repo
- [ ] `docs/architecture.md` con diagrama básico
- [ ] 3 ADRs de decisiones más importantes
- [ ] Tests documentados o plan de adopción
- [ ] Primera feature nueva usando SDD completo

### Primer mes
- [ ] Specs retroactivas de features más importantes
- [ ] `.claude/getting-started/` adaptado al proyecto
- [ ] CI/CD documentado en CLAUDE.md
- [ ] Deuda técnica documentada y priorizada
