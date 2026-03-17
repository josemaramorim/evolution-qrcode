# Deploy no GitHub Pages (Próximos passos)

Seu repositório local já está pronto! Agora você precisa conectar ao GitHub e fazer push.

## Passo 1: Criar repositório no GitHub

1. Acesse [github.com/new](https://github.com/new)
2. Crie um repositório chamado `evolution-qrcode` (ou outro nome)
3. **NÃO** inicialize com README (deixe em branco)
4. Clique em **Create repository**

Você receberá uma URL tipo:
```
https://github.com/seu-usuario/evolution-qrcode.git
```

## Passo 2: Conectar ao GitHub e fazer push

No PowerShell, na pasta do projeto, execute:

```powershell
git remote add origin https://github.com/seu-usuario/evolution-qrcode.git
git branch -M main
git push -u origin main
```

Troque `seu-usuario` pela seu usuário GitHub.

## Passo 3: Ativar GitHub Pages

1. Acesse seu repositório no GitHub
2. Vá em **Settings → Pages**
3. Em "Source", selecione **"Deploy from a branch"**
4. Selecione branch: **main**
5. Selecione pasta: **/docs**
6. Clique em **Save**

Aguarde alguns segundos (status pode mostrar "building").

## Passo 4: Seu link está pronto!

Após ativar, GitHub Pages mostrará um link tipo:
```
https://seu-usuario.github.io/evolution-qrcode/
```

A página será servida automaticamente do arquivo `docs/index.html`.

## Verificar se funcionou

Abra o link acima no navegador. Você verá a página "Conectar WhatsApp" e não haverá mais erro de CORS!

---

**Dúvidas?** Após fazer push, me passa o link do repositório que valido tudo!
