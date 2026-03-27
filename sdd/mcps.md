---
title: "MCPs para SDD"
description: "Linear, Notion, Atlassian, Shortcut y herramientas de enforcement del pipeline SDD"
---

# MCPs para Spec-Driven Development

El workflow SDD se potencia combinando dos capas de herramientas MCP.

---

## Las dos capas

| Capa | Qué resuelve | Herramientas |
|------|-------------|--------------|
| **Spec storage** | Dónde viven las specs, issues, backlogs | Linear, Notion, Atlassian, Shortcut, GitHub |
| **Pipeline enforcement** | Que Claude respete la secuencia SDD en cada sesión | spec-workflow-mcp, cc-sdd |

---

## Capa 1: Spec Storage

### Linear — Recomendado si el equipo usa Linear

Oficial, production-ready. Soporte explícito para Claude Code desde mayo 2025.

```bash
claude mcp add --transport http linear https://mcp.linear.app/mcp
```
Autentica via OAuth — no necesita API key.

**Docs:** https://linear.app/docs/mcp

---

### Notion — Para specs como documentos de diseño

Oficial, production-ready. Leer/escribir páginas, bases de datos y búsqueda.

```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

**Docs:** https://developers.notion.com/docs/mcp

---

### Atlassian (Jira + Confluence)

Oficial (beta). Jira issues + Confluence pages en un solo servidor.

```bash
claude mcp add atlassian https://mcp.atlassian.com/mcp \
  --header "Authorization: Bearer TU_API_TOKEN"
```

Generar token: https://id.atlassian.com/manage-profile/security/api-tokens

**Docs:** https://support.atlassian.com/atlassian-rovo-mcp-server/

---

### Shortcut

Oficial, production-ready. Claude Code como cliente de primera clase.

```bash
claude mcp add --transport http shortcut https://mcp.shortcut.com/mcp
```

---

### GitHub Projects (ya configurado)

El plugin `github` ya habilitado soporta GitHub Projects. Es la opción de menor fricción si el equipo ya usa GitHub para todo.

---

## Capa 2: Pipeline Enforcement

### spec-workflow-mcp — Con dashboard visual

Enforcea: Requirements → Design → Tasks → Implementation con aprobaciones entre fases.

```bash
claude mcp add-json "spec-workflow" '{
  "command": "npx",
  "args": ["-y", "@pimzino/spec-workflow-mcp@latest", "/ruta/proyecto"]
}'
```

**GitHub:** https://github.com/Pimzino/spec-workflow-mcp

<Info>
Ideal para equipos nuevos en SDD — el dashboard hace visible el proceso y obliga a respetar las fases.
</Info>

---

### cc-sdd — Zero infraestructura

No requiere servidor. Agrega steering files y slash commands al repo.

```bash
npx cc-sdd init
```

Crea `.claude/commands/spec.md` y `steering/` con los archivos de requirements, design y tasks.

**GitHub:** https://github.com/gotalab/cc-sdd

---

## Recomendación por situación

<CardGroup cols={2}>
  <Card title="Equipo nuevo en SDD" icon="graduation-cap">
    `spec-workflow-mcp` + `github`

    El dashboard hace visible el proceso.
  </Card>
  <Card title="Equipo con Linear" icon="list-check">
    `Linear MCP` + `cc-sdd` + `github`

    Spec en Linear → pipeline en cc-sdd → código en GitHub.
  </Card>
  <Card title="Empresa con Jira + Confluence" icon="building">
    `Atlassian Rovo` + `spec-workflow-mcp` + `github`
  </Card>
  <Card title="Setup mínimo (sin infraestructura extra)" icon="bolt">
    `superpowers` + specs en markdown en el repo

    Los skills de superpowers cubren el pipeline SDD sin herramientas externas.
  </Card>
</CardGroup>

---

## Agregar MCPs externos al proyecto

En `.mcp.json` en la raíz del repo:

```json
{
  "mcpServers": {
    "linear": {
      "type": "http",
      "url": "https://mcp.linear.app/mcp"
    },
    "notion": {
      "type": "http",
      "url": "https://mcp.notion.com/mcp"
    }
  }
}
```

Luego: `/reload-plugins`
