---
title: "SDD Engineering Guide"
description: "Guía completa para equipos C# backend que trabajan con Spec-Driven Development y Claude Code"
---

# SDD Engineering Guide

Guía de onboarding y workflow para equipos de desarrollo backend usando **ASP.NET Core · PostgreSQL · MongoDB · Docker · Claude Code**.

---

## ¿Qué es Spec-Driven Development?

Spec-Driven Development (SDD) es la práctica de **escribir una especificación detallada antes de tocar código**. Con Claude Code esto se convierte en un workflow estructurado que elimina el "vibe coding" y produce código más predecible, revisable y mantenible.

```
Ticket / Issue
    ↓
Brainstorming — explorar el problema
    ↓
Spec — qué hacer, cómo, en qué orden
    ↓
TDD — tests primero
    ↓
Implementación — siguiendo la spec
    ↓
Code Review — verificar contra la spec
    ↓
PR → Merge
```

---

## Secciones de esta guía

<CardGroup cols={2}>
  <Card title="Setup del entorno" icon="computer" href="/setup/system">
    Instalación de Git, .NET 8, Docker, IDE y bases de datos
  </Card>
  <Card title="Claude Code" icon="robot" href="/claude-code/installation">
    Instalación, plugins MCP y configuración para C# backend
  </Card>
  <Card title="Workflow SDD" icon="diagram-project" href="/sdd/workflow">
    El ciclo completo: brainstorming → spec → TDD → PR
  </Card>
  <Card title="Convenciones" icon="book" href="/conventions/commits">
    Commits, ramas, PRs y estructura de proyecto
  </Card>
</CardGroup>

---

## Stack cubierto

| Tecnología | Versión | Uso |
|-----------|---------|-----|
| ASP.NET Core | 8 | API host, controllers, middleware |
| Entity Framework Core | 8 | ORM + migraciones |
| PostgreSQL | 16 | Base de datos relacional |
| MongoDB | 7 | Base de datos documental |
| xUnit + Testcontainers | latest | Integration tests con DB real |
| Docker | 24+ | Entorno local y CI |
| Claude Code | latest | AI-assisted development |

---

## Tiempo estimado de setup

| Sección | Tiempo |
|---------|--------|
| Herramientas del sistema | 30–45 min |
| IDE y extensiones | 15–20 min |
| Docker + bases de datos | 20–30 min |
| Git | 10–15 min |
| Claude Code + plugins | 20–30 min |
| **Total** | **~2 horas** |
