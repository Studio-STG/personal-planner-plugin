---
description: Retoma ou reabre uma tarefa no Personal Planner.
argument-hint: <task_id>
---

Argumento do usuário: $ARGUMENTS

Parse:
- Se argumento for número → `task_id`
- Se vazio → chame `personal-planner:list-tasks-tool` sem filtro e apresente tarefas com status `pending` ou `completed` para o usuário escolher

Lógica:
- Se status da tarefa for `pending` → chame `personal-planner:start-task-tool` (retomar após pausa)
- Se status da tarefa for `completed` → chame `personal-planner:reopen-task-tool` para reabrir, depois pergunte se quer iniciar imediatamente ("sim" → chama `start-task-tool`)
- Se status for `in_progress` → informe "Tarefa já está em andamento."

Para descobrir o status sem que o usuário informe:
1. Chame `personal-planner:list-tasks-tool` para o dia de hoje
2. Encontre a tarefa pelo ID
3. Execute a ação correta conforme lógica acima

Após ação, confirme: "Tarefa #<id> retomada/reaberta. Status: <status>."
