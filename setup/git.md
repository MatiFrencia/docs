---
title: "Git y repositorios"
description: "SSH keys, configuración global y conexión con GitHub, GitLab o Azure DevOps"
---

# Git y repositorios

---

## Configuración global

```bash
git config --global user.name "Tu Nombre Completo"
git config --global user.email "tu@empresa.com"
git config --global core.editor "code --wait"
git config --global diff.tool vscode
git config --global difftool.vscode.cmd 'code --wait --diff $LOCAL $REMOTE'
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
git config --global core.autocrlf true
git config --global push.default current
git config --global credential.helper manager
```

---

## Generar SSH key

```bash
# Ed25519 — algoritmo recomendado
ssh-keygen -t ed25519 -C "tu@empresa.com"

# Agregar al ssh-agent (PowerShell como admin)
Get-Service ssh-agent | Set-Service -StartupType Automatic
Start-Service ssh-agent
ssh-add ~/.ssh/id_ed25519

# Ver la clave pública para copiar a GitHub/GitLab/Azure DevOps
cat ~/.ssh/id_ed25519.pub
```

---

## Conectar con el proveedor de Git

<Tabs>
  <Tab title="GitHub">
    1. Ir a https://github.com/settings/keys → **New SSH key**
    2. Title: `PC empresa - [tu nombre]`, Key type: Authentication
    3. Pegar el contenido de `id_ed25519.pub`

    ```bash
    ssh -T git@github.com
    # Hi username! You've successfully authenticated...
    ```
  </Tab>
  <Tab title="GitLab">
    1. Ir a `https://gitlab.tuempresa.com/-/profile/keys`
    2. Pegar la clave pública

    ```bash
    ssh -T git@gitlab.tuempresa.com
    # Welcome to GitLab, @username!
    ```
  </Tab>
  <Tab title="Azure DevOps">
    1. Ir a `https://dev.azure.com/empresa/_usersSettings/keys`
    2. **+ New Key** → pegar la clave pública

    ```bash
    ssh -T git@ssh.dev.azure.com
    # remote: Shell access is not supported.
    # (Este mensaje es normal — la auth funcionó)
    ```
  </Tab>
</Tabs>

---

## .gitignore para proyectos C#

```bash
dotnet new gitignore
```

Agregá además estas exclusiones críticas:

```gitignore
# Secrets y environment — NUNCA commitear
.env
.env.local
*.env
appsettings.Development.json

# Claude Code — overrides personales
.claude/settings.local.json

# Docker overrides locales
docker-compose.override.yml

# Build
bin/
obj/
*.user
.vs/
.idea/
TestResults/
```

---

## GitHub Personal Access Token (para Claude Code MCP)

El plugin `github` de Claude Code necesita un PAT:

1. Ir a https://github.com/settings/tokens → **Generate new token (classic)**
2. Scopes: `repo`, `read:org`, `workflow`
3. Guardarlo en `~/.claude/settings.json`:

```json
{
  "env": {
    "GITHUB_PERSONAL_ACCESS_TOKEN": "<your-github-pat>",
    "GITHUB_TOKEN": "<your-github-pat>"
  }
}
```
