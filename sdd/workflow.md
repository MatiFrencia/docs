---
title: "Spec-Driven Development"
description: "El workflow completo: brainstorming → spec → TDD → implementación → review → PR"
---

# Spec-Driven Development

---

## El flujo completo

<Steps>
  <Step title="Brainstorming">
    Explorar el problema antes de proponer soluciones.
  </Step>
  <Step title="Spec">
    Escribir qué hacer, cómo y en qué orden — antes de tocar código.
  </Step>
  <Step title="TDD">
    Escribir los tests que describen el comportamiento esperado.
  </Step>
  <Step title="Implementación">
    Implementar paso a paso siguiendo la spec, con checkpoints.
  </Step>
  <Step title="Code Review">
    Verificar que lo implementado cumple la spec original.
  </Step>
  <Step title="PR">
    Commit + push + Pull Request siguiendo las convenciones del equipo.
  </Step>
</Steps>

---

## Fase 1: Brainstorming

**Activarlo en Claude Code:**
```
Quiero implementar [descripción]. Antes de arrancar, hagamos brainstorming.
```

Claude usa el skill `superpowers:brainstorming` automáticamente al detectar trabajo creativo/nuevo.

**Qué explora:**
- ¿Qué problema resuelve exactamente?
- ¿Qué casos edge existen?
- ¿Qué partes del codebase actual afecta?
- ¿Hay múltiples approaches? ¿Trade-offs?
- ¿Qué información falta para implementar correctamente?

---

## Fase 2: Escribir la spec

**Activarlo:**
```
Con lo que discutimos, escribí el plan de implementación.
```

**Qué contiene una buena spec** (usar `specs/_template.md`):
- Problema y objetivos
- Fuera de scope (explícito)
- Cambios en la API
- Cambios en la base de datos
- Lógica de negocio y reglas
- Plan de tests
- Acceptance criteria

**Dónde guardar la spec:**
```
specs/wip/[número]-[nombre-feature].md
```

<Warning>
**Regla fundamental:** Si no existe la spec, no hay implementación. Crearla primero siguiendo `specs/_template.md`.
</Warning>

---

## Fase 3: TDD

```
Implementá el test de integración para [el endpoint / la lógica].
```

El ciclo:
1. **Red** — Claude escribe el test que describe el comportamiento esperado
2. **Green** — Claude implementa el código mínimo para pasar el test
3. **Refactor** — Claude limpia el código sin romper tests

```bash
# Verificar antes de continuar al siguiente paso
dotnet test MiProyecto.IntegrationTests
```

---

## Fase 4: Ejecutar el plan

```
Ejecutá el plan paso a paso. Esperá mi OK entre cada paso.
```

El skill `superpowers:executing-plans` guía la implementación con checkpoints entre pasos.

**Tips:**
- Pedile a Claude que lea el código existente antes de modificarlo
- Si algo no cierra con el plan, pausá y renegociá antes de seguir
- Usá `/fast` para pasos mecánicos (boilerplate)

---

## Fase 5: Code Review

```
/review-pr
```

El `pr-review-toolkit` lanza agentes especializados:

| Agente | Qué revisa |
|--------|-----------|
| `code-reviewer` | Bugs, lógica incorrecta, seguridad |
| `pr-test-analyzer` | Cobertura de tests, casos edge sin testear |
| `silent-failure-hunter` | Errores silenciosos, catch blocks problemáticos |
| `type-design-analyzer` | Diseño de tipos y modelos |
| `comment-analyzer` | Exactitud de comentarios |

---

## Fase 6: PR

```
/commit-push-pr
```

Esto: stagea → genera commit con la convención → push → crea PR en GitHub.

---

## Cuándo saltarse partes del workflow

| Situación | Qué podés omitir |
|-----------|-----------------|
| Hotfix urgente de producción | Brainstorming (pero sí spec breve y tests) |
| Cambio de config / chore | Brainstorming y TDD |
| Documentación | Todo excepto commit |
| Feature pequeña y aislada | Brainstorming breve, el resto completo |

---

## Reglas del SDD

1. **No hay "implementación rápida"** — si no hay spec, no hay código
2. **El plan es negociable, el scope no** — si surge algo fuera del plan, pausar y re-planificar
3. **Los tests van primero** — aunque parezca más lento, evita bugs y facilita refactors
4. **El CLAUDE.md es la fuente de verdad** — si cambia la arquitectura, actualizar inmediatamente
5. **Review antes de merge** — siempre, aunque sea tu propio código
