---
description: Testa conexão MCP com o Personal Planner e lista tools disponíveis.
---

Use a tool MCP `personal-planner:list-tasks-tool` (ou qualquer tool do server `personal-planner`) com um payload mínimo pra validar que o servidor está respondendo.

Reporte:
- Status da conexão (OK / 401 / 403 / 429 / offline)
- Tools disponíveis
- Token expirado? Recomende `/plugin reconfigure personal-planner` se 401.

Se 403 em tool específica, indique qual escopo está faltando e instrua a recriar o token no app.
