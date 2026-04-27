---
description: Edita campos de uma tarefa existente no Personal Planner.
argument-hint: <task_id> [campo=valor ...]
---

Argumento do usuário: $ARGUMENTS

Parse do argumento livre:
- Primeiro token numérico = `task_id`
- Tokens restantes — detecte pares chave=valor ou linguagem natural:
  - `desc="..."` ou `descrição=...` → `description`
  - `estimate=HH:MM` ou `estimativa=HH:MM` → `time_estimate`
  - `cat=<id>` ou `categoria=<id>` → `category_id`
  - `jira=ABC-123` ou `jira_id=...` → `jira_task_id`

Exemplos de entrada válida:
- `42 desc="Revisar PR do módulo de auth"`
- `42 estimate=01:30`
- `42 jira=PROJ-99 estimate=02:00`
- `42 descrição nova desc aqui` (linguagem natural → description)

Se nenhum campo for detectado além do ID, liste os campos atuais da tarefa (via `list-tasks-tool`) e pergunte o que editar.

Chame `personal-planner:update-task-tool` com `task_id` e apenas os campos alterados.

Após editar, mostre resumo:
```
Tarefa #<id> atualizada:
- Descrição: <description>
- Estimativa: <time_estimate>
- Categoria: <category>
- Jira: <jira_task_id>
```
