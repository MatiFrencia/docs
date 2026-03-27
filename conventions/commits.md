---
title: "Convención de commits"
description: "Formato, tipos, secciones obligatorias/opcionales y ejemplos completos"
---

# Convención de commits

---

## Estructura general

```
<type>: <descripción corta>

<párrafo de apertura>

## What it does
<explicación detallada>

## <Secciones opcionales>

## Files
<archivos modificados>

resolve: <link al ticket>
```

---

## Tipos válidos

| Tipo | Cuándo usarlo |
|------|--------------|
| `feature` | Nueva funcionalidad |
| `fix` | Corrección de un bug |
| `enhancement` | Mejora a funcionalidad existente |
| `hotfix` | Fix urgente a producción |
| `refactor` | Restructuración sin cambio de comportamiento |
| `chore` | Mantenimiento: deps, config, scripts, CI |
| `test` | Agregar o corregir tests sin tocar código de producción |
| `docs` | Solo cambios en documentación |

---

## Secciones del body

<AccordionGroup>
  <Accordion title="Párrafo de apertura — OBLIGATORIO">
    Una o dos oraciones que describen el cambio a nivel de negocio/producto.
    Legible por alguien que no conoce el código.

    ```
    Adds POST /internal/packages/renotify/batch, an internal endpoint that
    allows operators to manually renotify a list of packages to the sorter.
    ```
  </Accordion>

  <Accordion title="## What it does — OBLIGATORIO">
    Lista numerada del flujo de implementación, orientada a developers.

    ```markdown
    ## What it does
    1. Queries Order API to get service_id and zone.
    2. Builds a NotificationDTO using the existing mapper.
    3. Publishes to the retry queue via Dispatch — no HTTP call.
    ```
  </Accordion>

  <Accordion title="## Processing model — OPCIONAL">
    Incluir cuando hay concurrencia, workers, background jobs o caching.

    ```markdown
    ## Processing model
    Parallel worker pool using goroutines and channels.
    maxWorkers configurable via api_config.json.
    ```
  </Accordion>

  <Accordion title="## Response — OPCIONAL">
    Incluir cuando se agrega o modifica un endpoint.

    ```markdown
    ## Response
    Always 200 with summary: { total, succeeded, failed[{barcode, reason}] }
    Partial failures do not abort the batch.
    ```
  </Accordion>

  <Accordion title="## Request validation — OPCIONAL">
    Incluir cuando se agrega validación de inputs.

    ```markdown
    ## Request validation (400)
    - logistic_center_id missing or empty
    - barcodes exceeds max_barcodes_per_request
    ```
  </Accordion>

  <Accordion title="## Breaking changes — OPCIONAL">
    Incluir cuando se rompe compatibilidad hacia atrás.

    ```markdown
    ## Breaking changes
    - Removed field `legacy_id` from PackageDTO response.
    ```
  </Accordion>

  <Accordion title="## Notes — OPCIONAL">
    Contexto adicional, decisiones de diseño, deuda técnica generada.
  </Accordion>

  <Accordion title="## Files — OBLIGATORIO">
    Lista de archivos modificados con su rol. Siempre el último antes del `resolve:`.

    ```markdown
    ## Files
    - src/batch/usecase.go: worker pool use case
    - src/batch/handler.go: HTTP handler
    - src/bootstrap.go: wiring and route registration
    ```
  </Accordion>

  <Accordion title="resolve: — OBLIGATORIO">
    Última línea del commit. Link directo al ticket de Jira. Sin sección Markdown, sin punto final.

    ```
    resolve: https://empresa.atlassian.net/browse/PROJ-123
    ```
  </Accordion>
</AccordionGroup>

---

## Ejemplo completo — feature

```
feature: add batch renotification endpoint for package sorter

Adds POST /internal/packages/renotify/batch, an internal endpoint that
allows operators to manually renotify a list of packages to the sorter.

## What it does
1. Queries Order API (orderclient) to get service_id and zone.
2. Builds a NotificationDTO using the existing mapper, injecting
   the logistic_center_id provided by the caller.
3. Publishes to the retry BigQueue topic via Dispatch —
   no HTTP call to ucs-sorter.

## Processing model
Parallel worker pool using goroutines and channels.
maxWorkers and maxBarcodesPerRequest configurable via api_config.json.

## Response
Always 200 with summary: { total, succeeded, failed[{barcode, reason}] }
Failure reasons: not_found | api_error | queue_error
Partial failures do not abort the batch.

## Request validation (400)
- logistic_center_id missing or empty
- barcodes empty or exceeds max_barcodes_per_request

## Files
- config/config.go: BatchRenotificationConfig struct + getter
- internal/batch/usecase/batch_renotify.go: worker pool use case
- internal/batch/usecase/batch_renotify_test.go: 6 unit tests
- internal/batch/endpoint/batch_renotify.go: HTTP handler
- internal/batch/endpoint/batch_renotify_test.go: 5 handler tests
- internal/bootstrap.go: wiring and route registration

resolve: https://empresa.atlassian.net/browse/PROJ-456
```

---

## Ejemplo completo — fix

```
fix: prevent racing of concurrent requests

Introduces a request ID and a reference to the latest active request.
Dismisses incoming responses that do not belong to the latest request.

## What it does
1. Assigns a unique ID to each outgoing request at dispatch time.
2. Stores the ID of the most recently dispatched request in state.
3. On response arrival, discards if IDs don't match.

## Notes
Removes timeouts previously used to mitigate the racing issue.
Those timeouts are now obsolete and were adding unnecessary latency.

## Files
- src/client/request.ts: adds requestId generation and tracking
- src/client/response-handler.ts: adds ID comparison on response
- src/client/request.test.ts: 4 unit tests for ID tracking

resolve: https://empresa.atlassian.net/browse/PROJ-123
```

---

## Reglas

1. Descripción corta en inglés, minúscula, sin punto final, máximo 72 caracteres
2. Línea en blanco obligatoria entre header y párrafo de apertura
3. `## Files` siempre último antes del `resolve:`
4. `resolve:` siempre la última línea absoluta
5. No usar `feat:` — esta convención reemplaza Conventional Commits
