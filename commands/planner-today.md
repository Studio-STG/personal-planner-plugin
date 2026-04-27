---
description: Lista tarefas de hoje no Personal Planner com métricas (meta, planejado, registrado, %).
---

Execute em paralelo:

1. Tool `personal-planner:list-tasks-tool` sem args (pega hoje)
2. Tool `personal-planner:get-metrics-tool` sem args (pega hoje)

Apresente em formato compacto:

```
📅 <data>

Métricas:
- Meta: <daily_goal>
- Planejado: <planned_time>
- Registrado: <logged_hours> (<goal_percentage>%)

Tarefas (<count>):
[status] <descrição> — <time_estimate> (cat: <category>)
...
```

Use emoji de status: ⏳ pending · ▶️ in_progress · ✅ completed.

Se nenhuma tool retornar, instrua usuário a rodar `/planner-status` pra diagnóstico.
