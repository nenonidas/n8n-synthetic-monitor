# 🚀 Como Publicar no GitHub — Passo a Passo

## Passo 1 — Criar conta no GitHub (se ainda não tem)

1. Acesse: https://github.com/signup
2. Crie sua conta com o email `alerodriguesdesouza@gmail.com`
3. Confirme o email

---

## Passo 2 — Instalar o Git no WSL2

```bash
sudo apt update && sudo apt install -y git
```

Configure com seus dados:

```bash
git config --global user.name "Ale Rodrigues de Souza"
git config --global user.email "alerodriguesdesouza@gmail.com"
```

---

## Passo 3 — Criar o repositório no GitHub

1. Acesse: https://github.com/new
2. Preencha:
   - **Repository name:** `n8n-synthetic-monitor`
   - **Description:** `Monitoramento sintético de APIs com n8n — alertas inteligentes por email com templates HTML profissionais`
   - **Visibilidade:** Public
   - **NÃO** marque "Add a README" (já temos o nosso)
   - **NÃO** marque "Add .gitignore" (já temos)
   - **NÃO** marque "Choose a license" (já temos)
3. Clique em **Create repository**
4. Deixe a página aberta — vamos usar a URL

---

## Passo 4 — Autenticar com o GitHub

O GitHub não aceita mais senha via terminal. Use um **Personal Access Token**:

1. Acesse: https://github.com/settings/tokens?type=beta
2. Clique em **"Generate new token"**
3. Preencha:
   - **Token name:** `wsl2-push`
   - **Expiration:** 90 days
   - **Repository access:** All repositories
   - **Permissions → Repository:** selecione `Contents` (Read and write)
4. Clique em **Generate token**
5. **Copie o token** (ele não vai aparecer de novo!)

Para não ter que digitar toda vez, configure o cache:

```bash
git config --global credential.helper store
```

Na primeira vez que fizer `git push`, ele vai pedir user/senha.
- **Username:** `alerodriguesdesouza`
- **Password:** cole o token (não sua senha do GitHub)

---

## Passo 5 — Adicionar screenshots (opcional mas recomendado)

Antes de publicar, é legal adicionar imagens ao repositório.

### Banner do projeto

Crie uma imagem simples (pode usar o Canva gratuitamente) com:
- Tamanho: 1200x400 pixels
- Texto: "n8n Synthetic Monitor"
- Ícones de API, alerta, email
- Salve como `docs/images/banner.png`

### Preview do email

1. Abra o `templates/alerta-email.html` no navegador
2. Tire um screenshot
3. Salve como `docs/images/email-preview.png`

Se não quiser criar imagens agora, tudo bem — o README funciona sem elas (as imagens simplesmente não aparecem).

---

## Passo 6 — Publicar!

Copie os arquivos para o WSL2 (ajuste o caminho do download):

```bash
cp -r /mnt/c/Users/SEU_USUARIO/Downloads/file/* ~/n8n-synthetic-monitor/
```

Agora os comandos Git:

```bash
cd ~/n8n-synthetic-monitor

# Inicializar repositório
git init

# Adicionar todos os arquivos
git add .

# Verificar o que vai ser commitado
git status

# Primeiro commit
git commit -m "feat: workflow n8n de synthetic monitoring com template de alerta HTML"

# Conectar ao GitHub (troque pela URL do SEU repositório)
git remote add origin https://github.com/alerodriguesdesouza/n8n-synthetic-monitor.git

# Definir branch principal
git branch -M main

# Enviar para o GitHub
git push -u origin main
```

---

## Passo 7 — Adicionar tópicos ao repositório

Depois de publicar, volte à página do repositório no GitHub:

1. Clique na engrenagem ⚙️ ao lado de "About"
2. Em **Topics**, adicione:
   - `n8n`
   - `synthetic-monitoring`
   - `api-monitoring`
   - `uptime`
   - `alerting`
   - `email-template`
   - `devops`
   - `sre`
3. Em **Description**, confirme:
   - `Monitoramento sintético de APIs com n8n — alertas inteligentes por email com templates HTML profissionais`
4. Clique em **Save changes**

Isso ajuda outras pessoas a encontrarem seu projeto.

---

## Passo 8 — Compartilhar

Seu repositório estará em:

```
https://github.com/alerodriguesdesouza/n8n-synthetic-monitor
```

Compartilhe no LinkedIn, Twitter/X, ou comunidades:
- Reddit: r/n8n, r/devops, r/selfhosted
- Comunidade n8n: https://community.n8n.io
- Dev.to: escreva um artigo sobre o projeto

---

## Dica: Atualizações futuras

Sempre que fizer alterações:

```bash
cd ~/n8n-synthetic-monitor

# Ver o que mudou
git status

# Adicionar mudanças
git add .

# Commitar com mensagem descritiva
git commit -m "feat: adiciona suporte a múltiplos endpoints"

# Enviar
git push
```

---

## Resumo dos comandos

| Passo | Comando | O que faz |
|-------|---------|-----------|
| Config | `git config --global user.name "..."` | Configura seu nome |
| Config | `git config --global user.email "..."` | Configura seu email |
| Início | `git init` | Cria repositório local |
| Stage | `git add .` | Adiciona arquivos ao commit |
| Commit | `git commit -m "mensagem"` | Salva snapshot dos arquivos |
| Remote | `git remote add origin URL` | Conecta ao GitHub |
| Push | `git push -u origin main` | Envia para o GitHub |
