# Pagina de Conexao Evolution + n8n

Este projeto entrega uma pagina de conexao para cliente final via webhook no n8n:

1. Cliente abre um link publico.
2. Digita o nome da instancia.
3. O sistema gera e exibe QR Code.
4. Cliente escaneia no WhatsApp e conecta.
5. Opcionalmente pode desconectar a instancia pela mesma pagina.

## Arquivos

- `web/conexao.html`: interface que o cliente acessa.
- `docs/fluxo-n8n.md`: passo a passo do workflow no n8n.
- `docs/CONFIGURACAO-URL.md`: como configurar a URL do n8n no HTML.
- `n8n/workflow-evolution-qrcode.json`: workflow pronto para importar no n8n.

## Fluxo recomendado

- **Webhook GET**: entrega a pagina HTML (`/webhook/evolution/connect`).
- **Webhook POST**: inicia conexao na Evolution (`/webhook/evolution/connect/start`).
- **Webhook GET**: consulta status e QR (`/webhook/evolution/connect/status`).
- **Webhook POST**: desconecta instancia (`/webhook/evolution/connect/disconnect`).

## Como usar rapido

1. No n8n, crie os 4 endpoints conforme guia em `docs/fluxo-n8n.md`.
2. Cole o HTML de `web/conexao.html` no retorno do webhook GET da pagina.
3. Edite `web/conexao.html` para configurar a URL do seu n8n (veja `docs/CONFIGURACAO-URL.md`).
4. Se nao tiver acesso a variaveis de ambiente, edite direto os 3 nodes HTTP com `https://SEU_HOST_EVOLUTION` e `apikey`.
5. Entregue o link GET da pagina ao cliente.

## Importacao direta no n8n

1. Abra o n8n e use **Import from File**.
2. Selecione `n8n/workflow-evolution-qrcode.json`.
3. Salve o workflow e ative em seguida.

## Erro "Failed to fetch"?

Se a pagina mostra "Failed to fetch", voce precisa configurar a URL do seu n8n.
Consulte `docs/CONFIGURACAO-URL.md` para 3 formas diferentes de fazer isso.
4. Opcional: em vez de variaveis de ambiente, configure uma credencial HTTP Header Auth com `apikey`.

## Seguranca (producao)

- Ative HTTPS no dominio do n8n.
- Restrinja CORS para seu dominio.
- Nunca exponha API key da Evolution no front-end.
- Use token temporario no `sessionId` (opcional, porem recomendado).
- Configure TTL para sessoes pendentes (ex.: 10 min).
