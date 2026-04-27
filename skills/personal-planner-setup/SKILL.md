---
name: personal-planner-setup
description: Ajuda o usuário a gerar token API e validar a conexão MCP com o Personal Planner. Trigger ao dizer "/personal-planner-setup", "configurar planner", "conectar planner", ou ao mencionar problemas de conexão MCP do planner.
---

# Personal Planner — Setup MCP

Skill auxiliar do plugin `personal-planner`. O plugin já registra o servidor MCP automaticamente via `userConfig` (URL + token coletados no install). Esta skill ajuda quando:

- Token expirou/foi revogado e precisa ser regenerado
- Usuário quer testar/diagnosticar conexão
- Usuário esqueceu como gerar token

## Fluxo

### 1. Diagnóstico

Pergunte: "Já gerou um token no app?" Se não, vá para passo 2. Se sim, teste a conexão:

```bash
curl -X POST <URL> \
  -H "Authorization: Bearer <TOKEN>" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

| Status | Causa | Ação |
|--------|-------|------|
| 200 + lista tools | Tudo OK | Pronto, encerre |
| 401 | Token inválido/revogado | Vá para passo 2 |
| 403 | Token sem escopos | Vá para passo 2 (recriar com escopos) |
| 429 | Rate limit | Aguarde 1 min, tente de novo |
| Connection refused | App offline ou URL errada | Confirme URL com usuário |

### 2. Gerar token

Instrua o usuário:

1. Abra <https://planejador.studiostg.com.br/dashboard/configuracoes> (ou URL da instância dev/self-hosted)
2. Clique na aba **Claude Code**
3. Clique em "Criar token", escolha um nome descritivo
4. **Marque os escopos** desejados:
   - `tasks:read` — listar tarefas
   - `tasks:write` — criar/iniciar/completar
   - `metrics:read` — ver métricas
   - `time:log` — registrar tempo manual
5. Clique em criar e **copie o token** (só aparece uma vez)

### 3. Atualizar token no plugin

O token vive no keychain do sistema (gravado no install do plugin). Pra trocar:

```
/plugin reconfigure personal-planner
```

Vai pedir os mesmos campos (URL + token). Cole o novo token.

Reinicie o Claude Code (`/quit` e abra de novo) pra MCP server reconectar com novo token.

### 4. Validar

Depois de reiniciar:

```
/mcp
```

Deve listar `personal-planner` como **connected**. Tools disponíveis (8 total):

- `list-tasks-tool`
- `create-task-tool`
- `start-task-tool`
- `pause-task-tool`
- `complete-task-tool`
- `reopen-task-tool`
- `update-task-tool`
- `get-metrics-tool`

## Segurança

- Escopo restringe o que o token pode fazer — sempre marque o mínimo necessário.
- Se vazar token: revogue em Settings > Claude Code > "Revogar". Acesso é cortado imediatamente.
- Rate limit no servidor: 60 req/min por token. Burst suspeito é logado.
- Toda chamada vai pra `mcp_audit_logs` (visível futuramente no app).
