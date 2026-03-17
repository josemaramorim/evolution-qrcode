# Configurar URL do n8n na Página

## Problema: Failed to fetch

Se você vê o erro "Failed to fetch" ao clicar em "Gerar QR Code", é porque a página não sabe onde ficar os seus webhooks do n8n.

## Solução: 3 formas

### Opção 1: Editar direto no HTML (mais facil)

1. Abra `web/conexao.html` em um editor de texto.
2. Procure por esta linha (perto do topo do `<script>`):

```javascript
const N8N_BASE_URL = "https://n8n.example.com";
```

3. Troque `https://n8n.example.com` pela URL do seu n8n. Exemplo:

```javascript
const N8N_BASE_URL = "https://seu-n8n.com";
```

ou se tiver porta customizada:

```javascript
const N8N_BASE_URL = "https://seu-n8n.com:5678";
```

4. Salve e entregue ao cliente.

### Opção 2: Passar como parâmetro na URL (mais profissional)

Você nao precisa editar o arquivo. Só passe a URL como parâmetro `?apiBase=`:

```
https://seu-n8n.com/webhook/evolution/connect?apiBase=https://seu-n8n.com
```

Exemplo com porta:

```
https://seu-n8n.com/webhook/evolution/connect?apiBase=https://seu-n8n.com:5678
```

### Opção 3: Hospedar a página no próprio n8n (mais seguro)

Se a página estiver hospedada no même n8n que os webhooks, use caminhos relativos:

1. Abra `web/conexao.html`.
2. Troque:

```javascript
const N8N_BASE_URL = "https://n8n.example.com";
```

por:

```javascript
const N8N_BASE_URL = window.location.origin;
```

Assim funcionará automaticamente onde quer que esteja.

## Passo a passo rápido

1. Identifique a URL do seu n8n (ex: `https://meu-n8n.com`).
2. Escolha uma das 3 opções acima.
3. Teste: abra a página, digite "test", clique em "Gerar QR Code".
4. Deve desaparecer o "Failed to fetch" e aparecer o status "QR Code gerado" ou similar.

## Verificação

Se ainda vir "Failed to fetch":
1. Abra o DevTools do navegador (F12).
2. Aba "Console" - veja a mensagem de erro exata.
3. Aba "Network" - clique em "Gerar QR Code" novamente e veja qual requisição falhou.
4. Te mostra se está tentando acessar a URL certa.
