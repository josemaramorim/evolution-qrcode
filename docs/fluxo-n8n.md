# Workflow n8n: Conectar Instancia (Evolution API)

Este guia monta um fluxo profissional em 4 endpoints.

## 1) Endpoint da pagina (cliente)

- Node: **Webhook**
- Method: `GET`
- Path: `evolution/connect`
- Response: via node **Respond to Webhook**
- Header: `Content-Type: text/html; charset=utf-8`
- Body: conteudo de `web/conexao.html`

Resultado: voce tera um link publico para enviar ao cliente.

## 2) Endpoint para iniciar conexao

- Node: **Webhook**
- Method: `POST`
- Path: `evolution/connect/start`
- Espera JSON com:

```json
{
  "instanceName": "cliente-acme"
}
```

### Cadeia sugerida de nodes

1. `Webhook (POST /start)`
2. `Code - Validar Entrada`
3. `HTTP Request - Iniciar Conexao na Evolution`
4. `Code - Normalizar Resposta`
5. `Respond to Webhook`

### Code: Validar Entrada

```javascript
const body = $json;
const instanceName = (body.instanceName || '').trim();

if (!instanceName) {
  throw new Error('instanceName e obrigatorio');
}

return [{ json: { instanceName } }];
```

### HTTP Request: Iniciar Conexao na Evolution

Use as credenciais no n8n, nunca no front-end.

- Method: `POST`
- URL: `https://SEU_HOST_EVOLUTION/instance/connect/{{$json.instanceName}}`
- Headers:
  - `apikey: SUA_API_KEY_AQUI`
  - `Content-Type: application/json`
- Send Body: `true`
- Body JSON: `{}`

Observacao: dependendo da versao da Evolution API, o endpoint pode variar.
Se necessario, ajuste para o endpoint equivalente da sua instalacao.

### Code: Normalizar Resposta

```javascript
const input = $json;

const normalized = {
  sessionId: input.sessionId || input.instance?.instanceName || input.instanceName,
  status: input.status || 'waiting',
  qrCode: input.base64 || input.qrCode || input.qrcode || null,
  connected: (input.status || '').toLowerCase() === 'connected'
};

return [{ json: normalized }];
```

### Respond to Webhook

- Response Code: `200`
- Response Body: `{{$json}}`

## 3) Endpoint para consultar status/qr

- Node: **Webhook**
- Method: `GET`
- Path: `evolution/connect/status`
- Query esperado:
  - `sessionId` (ou nome da instancia)

### Cadeia sugerida de nodes

1. `Webhook (GET /status)`
2. `Code - Validar Query`
3. `HTTP Request - Buscar Status na Evolution`
4. `Code - Normalizar Status`
5. `Respond to Webhook`

### Code: Validar Query

```javascript
const sessionId = ($json.query?.sessionId || '').trim();
if (!sessionId) {
  throw new Error('sessionId e obrigatorio');
}
return [{ json: { sessionId } }];
```

### HTTP Request: Buscar Status na Evolution

- Method: `GET`
- URL: `https://SEU_HOST_EVOLUTION/instance/connectionState/{{$json.sessionId}}`
- Headers:
  - `apikey: SUA_API_KEY_AQUI`

Se sua Evolution retornar QR por outro endpoint, adicione outro HTTP Request e combine no node Code.

### Code: Normalizar Status

```javascript
const state = ($json.instance?.state || $json.state || $json.status || 'waiting').toLowerCase();

return [{
  json: {
    status: state,
    connected: state === 'open' || state === 'connected',
    qrCode: $json.base64 || $json.qrcode || $json.qrCode || null
  }
}];
```

## 4) Endpoint para desconectar instancia

- Node: **Webhook**
- Method: `POST`
- Path: `evolution/connect/disconnect`
- Espera JSON com:

```json
{
  "sessionId": "cliente-acme",
  "instanceName": "cliente-acme"
}
```

### Cadeia sugerida de nodes

1. `Webhook (POST /disconnect)`
2. `Code - Validar Dados`
3. `HTTP Request - Desconectar na Evolution`
4. `Code - Normalizar Resposta`
5. `Respond to Webhook`

### Code: Validar Dados

```javascript
const body = $json;
const sessionId = (body.sessionId || body.instanceName || '').trim();

if (!sessionId) {
  throw new Error('sessionId ou instanceName e obrigatorio');
}

return [{ json: { sessionId } }];
```

### HTTP Request: Desconectar na Evolution

- Method: `DELETE`
- URL: `https://SEU_HOST_EVOLUTION/instance/logout/{{$json.sessionId}}`
- Headers:
  - `apikey: SUA_API_KEY_AQUI`

Observacao: em algumas versoes a rota pode ser diferente, como endpoint de `disconnect`.
Ajuste para a rota equivalente da sua Evolution.

### Code: Normalizar Resposta

```javascript
return [{
  json: {
    disconnected: true,
    sessionId: $json.sessionId || null,
    status: 'disconnected'
  }
}];
```

## 5) Como informar credenciais (sem acesso a variaveis de ambiente)

Se voce nao tem acesso ao ambiente do servidor n8n, use um destes dois modos:

1. **Direto no node HTTP Request** (mais simples)
  - URL base: `https://SEU_HOST_EVOLUTION`
  - Header `apikey`: `SUA_API_KEY_AQUI`
2. **Credencial HTTP Header Auth do n8n** (mais profissional)
  - Crie uma credencial com header `apikey`.
  - Selecione a credencial nos 3 nodes HTTP.
  - Mantenha apenas a URL no node.

Obs.: a API key fica protegida no n8n e nao aparece para o cliente final.

## 6) Teste funcional

1. Abra o endpoint `GET /webhook/evolution/connect`.
2. Digite `instanceName`.
3. Verifique se o `POST /start` retorna `sessionId`.
4. Confirme polling no `GET /status`.
5. Escaneie QR e valide transicao para `connected`.
6. Clique em desconectar e valide `POST /disconnect` com retorno `disconnected`.

## 7) Hardening recomendado

- Proteja o endpoint de pagina com token curto por cliente.
- Adicione rate-limit por IP no reverse proxy.
- Registre logs de conexao com timestamp e `instanceName`.
- Remova sessoes presas em estado `waiting` por mais de 10 minutos.
