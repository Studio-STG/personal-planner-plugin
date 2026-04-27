---
description: Testa conexão MCP com o Personal Planner e lista tools disponíveis.
---

Use a tool MCP `personal-planner:list-tasks-tool` (ou qualquer tool do server `personal-planner`) com um payload mínimo pra validar que o servidor está respondendo.

Reporte:
- Status da conexão (OK / 401 / 403 / 429 / offline)
- Tools disponíveis (esperado: 8 tools)
- Token expirado? Recomende `/plugin reconfigure personal-planner` se 401.

Tools esperadas:
- `list-tasks-tool` — listar tarefas
- `create-task-tool` — criar tarefa
- `start-task-tool` — iniciar tarefa (→ in_progress)
- `pause-task-tool` — pausar tarefa (→ pending, salva tempo parcial)
- `complete-task-tool` — finalizar tarefa (→ completed)
- `reopen-task-tool` — reabrir tarefa completada (→ pending)
- `update-task-tool` — editar descrição, estimativa, categoria, Jira ID
- `get-metrics-tool` — métricas do dia

Se 403 em tool específica, indique qual escopo está faltando e instrua a recriar o token no app.
