---
title: "Claude Code"
description: "Instalación, login, settings.json y estructura .claude/ del proyecto"
---

# Claude Code

---

## Instalación

```bash
npm install -g @anthropic-ai/claude-code
claude --version
```

## Login

```bash
claude
# La primera vez abre el browser para autenticarse con tu cuenta Anthropic
```

---

## settings.json global

En `~/.claude/settings.json` (Windows: `C:\Users\TuUsuario\.claude\settings.json`):

```json
{
  "autoUpdatesChannel": "latest",
  "env": {
    "GITHUB_PERSONAL_ACCESS_TOKEN": "<your-github-pat>",
    "GITHUB_TOKEN": "<your-github-pat>",
    "GREPTILE_API_KEY": "TU_GREPTILE_KEY"
  },
  "enabledPlugins": {
    "csharp-lsp@claude-plugins-official": true,
    "superpowers@claude-plugins-official": true,
    "github@claude-plugins-official": true,
    "context7@claude-plugins-official": true,
    "microsoft-docs@claude-plugins-official": true,
    "commit-commands@claude-plugins-official": true,
    "feature-dev@claude-plugins-official": true,
    "code-review@claude-plugins-official": true,
    "pr-review-toolkit@claude-plugins-official": true,
    "opsera-devsecops@claude-plugins-official": true,
    "coderabbit@claude-plugins-official": true,
    "claude-md-management@claude-plugins-official": true,
    "code-simplifier@claude-plugins-official": true,
    "greptile@claude-plugins-official": true,
    "explanatory-output-style@claude-plugins-official": true,
    "learning-output-style@claude-plugins-official": true,
    "postman@claude-plugins-official": false,
    "playwright@claude-plugins-official": false,
    "chrome-devtools-mcp@claude-plugins-official": false
  }
}
```

<Tip>
`postman`, `playwright` y `chrome-devtools-mcp` están en `false` porque suman ~160 tool schemas al context window. Habilitá con `/plugin` cuando los necesites puntualmente.
</Tip>

Después de editar:
```
/reload-plugins
/doctor
```

---

## ⚠ IMPORTANTE — El índice en CLAUDE.md

El `CLAUDE.md` de la raíz del proyecto debe tener un **índice al principio** con links a todos los archivos de documentación relevantes.

**Por qué es crítico:** Claude Code solo carga automáticamente el `CLAUDE.md` en cada sesión. Sin un índice explícito con links, el agente no sabe que existen specs, ADRs ni convenciones — y no los va a leer.

```markdown
## Índice de documentación del proyecto

| Documento | Qué encontrás |
|-----------|--------------|
| [`specs/wip/`](./specs/wip/) | Features en desarrollo — leer antes de implementar |
| [`specs/closed/`](./specs/closed/) | Historial de features completadas |
| [`.claude/mcp-guide.md`](./.claude/mcp-guide.md) | Qué plugin MCP usar para qué tarea |
| [`.claude/getting-started/README.md`](./.claude/getting-started/README.md) | Onboarding y convenciones |
| [`docs/`](./docs/) | Documentación técnica adicional |
```

El índice va **antes** de cualquier otro contenido en el CLAUDE.md.

---

## Estructura .claude/ del proyecto

```
.claude/
  settings.json         ← permisos del proyecto (se commitea)
  settings.local.json   ← overrides personales (en .gitignore)
  mcp-guide.md          ← referencia de plugins por prioridad
  getting-started/      ← esta documentación de onboarding
```

### .claude/settings.json del proyecto

```json
{
  "permissions": {
    "allow": [
      "Bash(dotnet:*)",
      "Bash(docker:*)",
      "Bash(docker compose:*)",
      "Bash(git log:*)",
      "Bash(git status:*)",
      "Bash(git diff:*)"
    ],
    "deny": [
      "Bash(git push --force:*)",
      "Bash(dotnet ef database drop:*)"
    ]
  }
}
```

---

## Comandos slash más usados

| Comando | Qué hace |
|---------|----------|
| `/commit` | Commit con mensaje descriptivo automático |
| `/commit-push-pr` | Commit + push + abre PR |
| `/review-pr` | Code review con agentes especializados |
| `/revise-claude-md` | Actualiza CLAUDE.md con aprendizajes de la sesión |
| `/reload-plugins` | Recarga plugins después de cambios en settings.json |
| `/doctor` | Diagnóstico de instalación |
| `/plugin` | Gestionar plugins |
| `/fast` | Toggle de fast mode |
