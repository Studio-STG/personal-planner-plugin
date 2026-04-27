# personal-planner-plugin

Plugin oficial do [Personal Planner](https://planejador.studiostg.com.br) para Claude Code.

Conecta o Claude Code ao seu planner via MCP — crie, inicie, complete tarefas e consulte métricas direto do terminal.

## Instalação

### 1. Adicionar marketplace

```
/plugin marketplace add github:jeffersonrucu/personal-planner-plugin
```

### 2. Instalar plugin

```
/plugin install personal-planner@personal-planner-marketplace
```

Claude Code vai pedir:
- **URL do servidor MCP** — default `https://planejador.studiostg.com.br/mcp`. Override pra dev/self-hosted.
- **Bearer Token** — gere em <https://planejador.studiostg.com.br/dashboard/configuracoes> > aba **Claude Code**.

Token é salvo no keychain do sistema (não vai pra `~/.claude/settings.json`).

### 3. Reiniciar Claude Code

```
/quit
```

Reabra. Pronto.

## Como gerar o token

1. Acesse <https://planejador.studiostg.com.br/dashboard/configuracoes>
2. Aba **Claude Code**
3. "Criar token", marque escopos:
   - `tasks:read` — listar tarefas
   - `tasks:write` — criar/iniciar/completar tarefas
   - `metrics:read` — ver métricas
   - `time:log` — registrar tempo manual
4. Copie o token exibido (só aparece uma vez)

## Comandos disponíveis

| Comando | O que faz |
|---------|-----------|
| `/personal-planner:planner-status` | Testa conexão + lista tools |
| `/personal-planner:planner-today` | Tarefas + métricas de hoje |
| `/personal-planner:planner-quick-task <desc> [HH:MM]` | Cria task rápida |
| `/personal-planner:personal-planner-setup` | Skill de troubleshoot/regerar token |

## Tools MCP expostas

- `list-tasks-tool` — listar tarefas
- `create-task-tool` — criar tarefa
- `start-task-tool` — iniciar tarefa (status → `in_progress`)
- `complete-task-tool` — finalizar tarefa
- `get-metrics-tool` — métricas do dia

## Uso natural

Não precisa lembrar das tools. Converse:

```
> lista minhas tasks de hoje
> cria task "revisar PR" com 1h de estimativa
> inicia a task ID 42
> qual minha meta diária hoje?
> finaliza a task que eu tô fazendo
```

## Atualizar token

Token expirou ou vazou? Revogue no app, gere novo, e:

```
/plugin reconfigure personal-planner
```

## Segurança

- Escopos limitam o que cada token pode fazer.
- Isolamento server-side: token de A não acessa tasks de B (validado via global scope).
- Rate limit: 60 req/min por token, 300/min por IP.
- Audit log persiste toda call (user, tool, params mascarados, status, IP).
- HTTPS obrigatório em produção.

Pra revogar: Settings > **Claude Code** > "Revogar". Acesso é cortado imediatamente.

## Troubleshooting

| Erro | Causa | Fix |
|------|-------|-----|
| `MCP error: 401` | Token inválido/revogado | `/plugin reconfigure personal-planner` |
| `MCP error: 403` | Escopo faltando | Recriar token no app marcando escopos corretos |
| `disconnected` | URL errada ou app offline | Confirmar URL e disponibilidade |
| Tools não aparecem | Cache | `/quit` e reabrir |

## Versão

`1.0.0` — primeira release pública.

## Licença

MIT
