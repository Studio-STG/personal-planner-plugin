---
description: Cria uma tarefa rápida no Personal Planner. Argumento livre = descrição.
argument-hint: <descrição da task> [estimate HH:MM]
---

Argumento do usuário: $ARGUMENTS

Parse:
- Tudo até o último token no formato `HH:MM` (ou sem ele) = `description`
- Se houver `HH:MM` no fim, use como `time_estimate`
- Se não houver, omita `time_estimate`

Chame `personal-planner:create-task-tool` com:
- `description`: extraído acima
- `time_estimate`: se aplicável

Após criar, confirme ID e descrição. Pergunte se quer iniciar agora ("sim" → chama `start-task-tool` com o ID retornado).
