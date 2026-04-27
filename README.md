# personal-planner-plugin

Plugin oficial do [Personal Planner](https://planejador.studiostg.com.br) para Claude Code.

Conecta o Claude Code ao seu planner via MCP — gerencie tarefas, controle tempo e consulte métricas direto do terminal.

---

## Instalação

### 1. Adicionar marketplace

```
/plugin marketplace add Studio-STG/personal-planner-plugin
```

### 2. Instalar plugin

```
/plugin install personal-planner@personal-planner-marketplace
```

Claude Code vai pedir:
- **URL do servidor MCP** — default `https://planejador.studiostg.com.br/mcp`
- **Bearer Token** — gere em <https://planejador.studiostg.com.br/dashboard/configuracoes> > aba **Claude Code**

### 3. Reiniciar Claude Code

```
/quit
```

Reabra. Pronto. Teste com `/personal-planner:planner-today`.

---

## Como gerar o token

1. Acesse <https://planejador.studiostg.com.br/dashboard/configuracoes>
2. Aba **Claude Code**
3. "Criar token" → nome descritivo → marque escopos:
   - `tasks:read` — listar tarefas
   - `tasks:write` — criar, iniciar, pausar, completar, reabrir, editar
   - `metrics:read` — consultar métricas
   - `time:log` — registrar tempo manual
4. Copie o token (só aparece uma vez)

---

## Comandos disponíveis

| Comando | O que faz |
|---------|-----------|
| `/personal-planner:planner-today` | Tarefas + métricas de hoje |
| `/personal-planner:planner-quick-task <desc> [HH:MM]` | Cria task rápida |
| `/personal-planner:planner-pause <id>` | Pausa tarefa em andamento |
| `/personal-planner:planner-resume <id>` | Retoma tarefa pausada ou reabre completada |
| `/personal-planner:planner-edit <id> [campos]` | Edita campos da tarefa |
| `/personal-planner:planner-status` | Testa conexão + lista tools |

---

## Exemplos passo a passo

### Ver tarefas e métricas do dia

```
/personal-planner:planner-today
```

**Retorno real:**
```
📅 2026-04-27

Métricas:
- Meta: 0h 00m
- Planejado: 0h 30m
- Registrado: 1h 21m (17%)

Tarefas (1):
✅ criando testes automatizados para setup — 00:30:00 (cat: Terra Zoo)
```

---

### Criar uma tarefa rápida

```
/personal-planner:planner-quick-task revisar PR do módulo de auth 01:30
```

Claude chama `create-task-tool`:

```json
{
  "created": true,
  "task": {
    "id": 7490,
    "description": "revisar PR do módulo de auth",
    "status": "pending",
    "time_estimate": "01:30:00"
  }
}
```

Claude pergunta: **"Quer iniciar agora?"**  
Responda `sim` → chama `start-task-tool` automaticamente.

---

### Iniciar uma tarefa

Opção 1 — ao criar via `planner-quick-task`, responder "sim".

Opção 2 — natural language:

```
inicia a tarefa 7490
```

Claude chama `start-task-tool`:

```json
{
  "started": true,
  "task_id": 7490,
  "started_at": "2026-04-27T09:46:55-03:00",
  "status": "in_progress"
}
```

---

### Pausar uma tarefa

```
/personal-planner:planner-pause 7490
```

ou natural:

```
pausa a task que estou fazendo
```

Claude chama `pause-task-tool`:

```json
{
  "paused": true,
  "task_id": 7490,
  "time_spent": "00:14:00",
  "status": "pending"
}
```

Tempo parcial é registrado. Pode retomar depois.

---

### Retomar uma tarefa pausada

```
/personal-planner:planner-resume 7490
```

Claude verifica status → `pending` → chama `start-task-tool`:

```json
{
  "started": true,
  "task_id": 7490,
  "started_at": "2026-04-27T10:05:00-03:00",
  "status": "in_progress"
}
```

---

### Finalizar uma tarefa

```
finaliza a task 7490
```

Claude chama `complete-task-tool`:

```json
{
  "completed": true,
  "task_id": 7490,
  "time_spent": "01:21:00",
  "status": "completed"
}
```

---

### Reabrir uma tarefa completada

```
/personal-planner:planner-resume 7490
```

Claude detecta status `completed` → chama `reopen-task-tool`:

```json
{
  "reopened": true,
  "task_id": 7490,
  "status": "pending"
}
```

Pergunta se quer iniciar imediatamente.

---

### Editar uma tarefa

```
/personal-planner:planner-edit 7490 estimate=02:00
```

```
/personal-planner:planner-edit 7490 desc="Revisar PR do auth + testes" jira=AUTH-42
```

```
/personal-planner:planner-edit 7490 estimativa nova de duas horas e meia
```

Claude chama `update-task-tool`:

```json
{
  "updated": true,
  "task": {
    "id": 7490,
    "description": "Revisar PR do auth + testes",
    "time_estimate": "02:30:00",
    "category": "Uncategorized",
    "jira_task_id": "AUTH-42",
    "status": "pending"
  }
}
```

---

### Consultar métricas

```
qual minha meta de hoje?
```

Claude chama `get-metrics-tool`:

```json
{
  "date": "2026-04-27",
  "daily_goal": "8h 00m",
  "planned_time": "6h 30m",
  "logged_hours": "3h 45m",
  "goal_percentage": 47
}
```

---

### Uso em linguagem natural

Não precisa lembrar dos comandos. Converse normalmente:

```
lista minhas tasks de hoje
cria task "revisar PR" com 1h30 de estimativa
inicia a task 42
pausa o que estou fazendo agora
qual minha meta diária?
finaliza a task que tô fazendo
edita a task 42, muda estimativa pra 2h
reabre a task 42
```

---

## Ciclo completo de uma tarefa

```
pending ──(start)──▶ in_progress ──(pause)──▶ pending
                          │
                       (complete)
                          │
                          ▼
                      completed ──(reopen)──▶ pending
```

---

## Tools MCP expostas

| Tool | Escopo | Ação |
|------|--------|------|
| `list-tasks-tool` | `tasks:read` | Lista tarefas do dia (filtro opcional por status) |
| `create-task-tool` | `tasks:write` | Cria nova tarefa |
| `start-task-tool` | `tasks:write` | Inicia cronômetro → `in_progress` |
| `pause-task-tool` | `tasks:write` | Pausa cronômetro → `pending` (tempo parcial salvo) |
| `complete-task-tool` | `tasks:write` | Finaliza → `completed` |
| `reopen-task-tool` | `tasks:write` | Reabre tarefa completada → `pending` |
| `update-task-tool` | `tasks:write` | Edita descrição, estimativa, categoria, Jira ID |
| `get-metrics-tool` | `metrics:read` | Meta diária, planejado, registrado, % |

---

## Segurança

- Escopos limitam o que cada token pode fazer.
- Isolamento server-side: token de A não acessa tasks de B.
- Rate limit: 60 req/min por token, 300/min por IP.
- Audit log persiste toda call (user, tool, params mascarados, status, IP).
- HTTPS obrigatório em produção.

Para revogar: Settings > **Claude Code** > "Revogar". Acesso cortado imediatamente.

---

## Atualizar token

Token expirou ou vazou? Revogue no app, gere novo:

```
/plugin reconfigure personal-planner
```

---

## Troubleshooting

| Erro | Causa | Fix |
|------|-------|-----|
| `MCP error: 401` | Token inválido/revogado | `/plugin reconfigure personal-planner` |
| `MCP error: 403` | Escopo faltando | Recriar token com escopos corretos |
| `disconnected` | URL errada ou app offline | Confirmar URL e disponibilidade |
| Tools não aparecem | Cache antigo | `/quit` e reabrir |
| `Tarefa precisa estar in_progress` | Status incorreto para a ação | Liste tasks com `/planner-today` e verifique status |

---

## Licença

MIT
