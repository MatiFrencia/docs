---
title: "IDE y extensiones"
description: "VS Code (recomendado), JetBrains Rider o Visual Studio 2022"
---

# IDE y extensiones

**VS Code es la opción recomendada** por su integración nativa con Claude Code y el plugin `csharp-lsp`.

---

## VS Code (recomendado)

Descargá desde https://code.visualstudio.com/ — marcá "Add to PATH" durante la instalación.

### Extensiones esenciales

```bash
# C# y .NET
code --install-extension ms-dotnettools.csharp
code --install-extension ms-dotnettools.csdevkit
code --install-extension ms-dotnettools.vscode-dotnet-runtime

# Git
code --install-extension eamodio.gitlens
code --install-extension mhutchie.git-graph

# Docker y bases de datos
code --install-extension ms-azuretools.vscode-docker
code --install-extension cweijan.vscode-postgresql-client2
code --install-extension mongodb.mongodb-vscode

# Calidad de código
code --install-extension EditorConfig.EditorConfig
code --install-extension usernamehw.errorlens
code --install-extension streetsidesoftware.code-spell-checker

# REST testing liviano
code --install-extension humao.rest-client
```

### Configuración recomendada

En `%APPDATA%\Code\User\settings.json`:

```json
{
  "editor.formatOnSave": true,
  "editor.tabSize": 4,
  "editor.rulers": [120],
  "editor.bracketPairColorization.enabled": true,
  "git.autofetch": true,
  "dotnet.automaticallyCreateSolutionInWorkspace": false,
  "[csharp]": {
    "editor.defaultFormatter": "ms-dotnettools.csharp"
  }
}
```

### Claude Code en VS Code

1. Buscá "Claude Code" en el Marketplace de VS Code
2. Instalá la extensión de Anthropic
3. Abrí el terminal integrado y ejecutá `claude` para iniciar una sesión

<Info>
El plugin `csharp-lsp` de Claude Code se conecta al Language Server de VS Code para dar diagnósticos en tiempo real dentro de las sesiones de Claude.
</Info>

---

## JetBrains Rider (alternativa)

Rider es el IDE más completo para C# fuera del ecosistema Microsoft. Requiere licencia.

1. Descargá JetBrains Toolbox desde https://www.jetbrains.com/toolbox-app/
2. Instalá Rider desde Toolbox
3. Plugins recomendados: **GitToolBox**, **.ignore**, **String Manipulation**

---

## Visual Studio 2022 (alternativa)

Descargá desde https://visualstudio.microsoft.com/vs/

Workloads a instalar:
- ✓ ASP.NET and web development
- ✓ Data storage and processing
- ✓ .NET desktop development
- ✓ Container development tools

---

## .editorconfig

Agregá en la raíz del repo para uniformizar el estilo entre IDEs:

```ini
root = true

[*]
indent_style = space
indent_size = 4
end_of_line = crlf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.{json,yml,yaml}]
indent_size = 2

[*.md]
trim_trailing_whitespace = false
```
