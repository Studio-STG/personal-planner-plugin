---
description: Pausa a tarefa em andamento no Personal Planner.
argument-hint: <task_id>
---

Argumento do usuário: $ARGUMENTS

Parse:
- Se argumento for número → `task_id`
- Se vazio → chame `personal-planner:list-tasks-tool` filtrando `status=in_progress` e apresente lista para o usuário escolher

Chame `personal-planner:pause-task-tool` com:
- `task_id`: ID da tarefa

Após pausar, confirme: "Tarefa #<id> pausada. Tempo parcial: <time_spent>. Status: pending."
