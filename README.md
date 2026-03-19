

<h1 align="center">n8n Synthetic Monitor</h1>

<p align="center">
  <strong>Monitoramento sintético de APIs com n8n </strong>
</p>

<p align="center">
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-como-funciona">Como Funciona</a> •
  <a href="#%EF%B8%8F-configuração">Configuração</a> •
  <a href="#-template-de-alerta">Template de Alerta</a> •
  <a href="#-contribuindo">Contribuindo</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/n8n-v1.0+-red?style=flat-square&logo=n8n" alt="n8n">
  <img src="https://img.shields.io/badge/license-MIT-green?style=flat-square" alt="License">
  <img src="https://img.shields.io/badge/status-production--ready-brightgreen?style=flat-square" alt="Status">
  <img src="https://img.shields.io/badge/alerts-email%20%7C%20slack%20%7C%20webhook-blue?style=flat-square" alt="Alerts">
  <img src="https://img.shields.io/github/stars/alerodriguesdesouza/n8n-synthetic-monitor?style=flat-square" alt="Stars">
</p>

---

## 📖 Sobre o Projeto

Este projeto oferece um **workflow n8n pronto para uso** que monitora a disponibilidade de APIs através de testes sintéticos periódicos. Quando uma falha é detectada, um **alerta HTML** é enviado por email com todos os detalhes do incidente.

**O que são testes sintéticos?** São requisições automáticas feitas em intervalos regulares para verificar se uma API ou uma URL estão respondendo corretamente. Diferente de monitoramento passivo (que depende de tráfego real), testes sintéticos detectam problemas **antes** que os usuários sejam impactados.

### Por que usar isto?

- **Zero custo** — roda no n8n (self-hosted ou cloud)
- **Envio de comunicação** — template HTML responsivo compatível com Gmail, Outlook e Apple Mail
- **Flexível** — monitore qualquer endpoint HTTP/HTTPS
- **Extensível** — adicione Slack, Teams, Discord, PagerDuty ou qualquer webhook

---

## 🏗 Arquitetura

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Schedule    │────▶│ HTTP Request │────▶│   Avaliar     │
│  (cron 5min) │     │  GET /api    │     │  Resposta     │
└──────────────┘     └──────────────┘     └──────┬───────┘
                                                  │
                                        ┌─────────┴─────────┐
                                        │                   │
                                   ✅ Saudável          ❌ Falha
                                        │                   │
                                   ┌────┴────┐      ┌──────┴──────┐
                                   │ Log OK  │      │Montar Alerta│
                                   └─────────┘      └──────┬──────┘
                                                           │
                                                 ┌─────────┼─────────┐
                                                 │         │         │
                                              📧 Email  💬 Slack  🔗 Webhook
```

---

## ⚡ Quick Start

### Pré-requisitos

- [n8n](https://n8n.io/) instalado (self-hosted ou cloud)
- Uma conta de email para envio de alertas (Gmail, Outlook, etc.)

### 3 passos para começar

```bash
# 1. Clone o repositório
git clone https://github.com/nenonidas/n8n-synthetic-monitor.git

# 2. Abra o n8n e importe o workflow
#    Menu lateral → Import from File → selecione workflows/n8n-synthetic-monitor.json

# 3. Configure suas credenciais e ative o workflow
#    Detalhes na seção "Configuração" abaixo
```

---

## 🔍 Como Funciona

### O Workflow

O workflow executa a cada **5 minutos** (configurável) e realiza:

1. **Requisição HTTP** para o endpoint configurado
2. **Validação tripla** da resposta:
   - **Status Code** — espera 2xx (200-399)
   - **Tempo de Resposta** — limite de 2 segundos (configurável)
   - **Corpo da Resposta** — verifica se não está vazio
3. **Decisão** — se todas as validações passam, registra log de sucesso
4. **Alerta** — se qualquer validação falha, monta mensagem detalhada e notifica

### Validações detalhadas

| Validação | Critério | Personalizável |
|-----------|----------|:--------------:|
| Status HTTP | 200-399 | ✅ |
| Tempo de resposta | < 2000ms | ✅ |
| Corpo não vazio | length > 0 | ✅ |

---

## ⚙️ Configuração

### Passo 1 — Instalar o n8n

Escolha uma das opções:

<details>
<summary><strong>🐳 Docker (recomendado)</strong></summary>

```bash
docker run -d \
  --name n8n \
  -p 5678:5678 \
  -v n8n_data:/home/node/.n8n \
  n8nio/n8n
```

Acesse: `http://localhost:5678`

OBS: Nesse ponto, caso esteja executando k8s através do wsl, quando tentar acessar o http://localhost:5678, o navegador devolva um ERR_CONNECTION, nesse caso, basta executar de dentro do WSL o comando 
**kubectl port-forward -n monitoring svc/n8n 5678:5678** Isso vai expor a porta do serviço do k8s e será possivel acessar via host windows.
</details>

<details>
<summary><strong>📦 npm</strong></summary>

```bash
npm install -g n8n
n8n start
```

</details>

<details>
<summary><strong>☁️ n8n Cloud</strong></summary>

Crie uma conta gratuita em [n8n.io/cloud](https://n8n.io/cloud)

</details>

### Passo 2 — Importar o Workflow

1. No n8n, vá para a **tela inicial** (ícone de casa)
2. Clique nos **três pontinhos (⋮)** no canto superior direito
3. Selecione **"Import from File"**
4. Escolha o arquivo `workflows/n8n-synthetic-monitor.json`
5. O workflow aparecerá com todos os nós conectados

> **Alternativa:** Use `Ctrl + O` em qualquer tela para importar rapidamente.

### Passo 3 — Configurar o Endpoint

1. Clique duas vezes no nó **"GET /teste"**
2. Altere o campo **URL** para sua API
3. Se precisar de autenticação, clique em **Add Option → Headers**:

```
Header Name:  Authorization
Header Value: Bearer seu-token-aqui
```

### Passo 4 — Configurar Alertas por Email

#### 4.1 Gerar Senha de App (Gmail)

1. Acesse: [myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords)
2. Digite um nome para o app (ex: `n8n`)
3. Clique em **Criar**
4. Copie a senha de 16 caracteres gerada

> ⚠️ Pré-requisito: [Verificação em duas etapas](https://myaccount.google.com/signinoptions/two-step-verification) deve estar ativada.

#### 4.2 Configurar SMTP no n8n

1. Clique duas vezes no nó **"Email Alert"**
2. **Habilite** o nó (toggle superior direito)
3. Em **Credential**, clique em **"Create new credential" → "SMTP"**
4. Preencha:

| Campo | Gmail | Outlook |
|-------|-------|---------|
| Host | `smtp.gmail.com` | `smtp.office365.com` |
| Port | `465` | `587` |
| SSL/TLS | Ativado | Ativado (STARTTLS) |
| User | seu email | seu email |
| Password | senha de app | sua senha |

5. Clique em **Save**

#### 4.3 Configurar Destinatário

| Campo | Valor |
|-------|-------|
| From | `seuemail@gmail.com` |
| To | `destinatario@empresa.com` |
| Subject | `🚨 ALERTA - API Indisponível` |
| Email Format | `HTML` |
| Message | conteúdo do `templates/alerta-email.html` |

### Passo 5 — Configurar Alertas Adicionais (Opcional)

<details>
<summary><strong>💬 Slack</strong></summary>

1. Crie um app em [api.slack.com/apps](https://api.slack.com/apps)
2. Adicione o scope `chat:write` em **OAuth & Permissions**
3. Instale no workspace e copie o **Bot Token** (`xoxb-...`)
4. No n8n, habilite o nó **"Slack Alert"** e configure a credential
5. Defina o canal (ex: `#alertas-monitoring`)

</details>

<details>
<summary><strong>🔗 Webhook (Teams / Discord / PagerDuty)</strong></summary>

| Serviço | Onde pegar o webhook |
|---------|---------------------|
| Teams | Canal → ⋮ → Connectors → Incoming Webhook |
| Discord | Server Settings → Integrations → Webhooks |
| PagerDuty | Services → Integrations → Events API v2 |

Habilite o nó **"Webhook Alert"** e cole a URL.

</details>

### Passo 6 — Testar

1. Clique em **"Test Workflow"** no canto inferior do canvas
2. Verifique o output de cada nó clicando neles
3. Para testar o caminho de falha, altere a URL para um endpoint inválido

### Passo 7 — Ativar

1. No canto superior direito, mude o toggle para **Active**
2. Pronto! O monitoramento está rodando automaticamente

---

## 📧 Template de Alerta

O projeto inclui um template HTML profissional para alertas por email, compatível com os principais clientes de email.



### O que o email inclui

- 🔴 Barra de severidade visual no topo
- 📋 Detalhes do incidente (endpoint, status, tempo de resposta, timestamp)
- ⚠️ Lista de problemas detectados
- 🔧 Ações recomendadas com comandos
- 📊 Botões de ação (Grafana, Logs)
- 📅 Timeline do incidente
- 🏷️ Badge de severidade (Crítico / Warning / Info)

### Compatibilidade

| Cliente | Suportado |
|---------|:---------:|
| Gmail | ✅ |
| Outlook | ✅ |
| Apple Mail | ✅ |
| Yahoo Mail | ✅ |
| Thunderbird | ✅ |

> O template usa apenas `<table>` e inline styles para máxima compatibilidade.

### Personalizar

Edite o arquivo `templates/alerta-email.html`:

- Altere cores, logo e textos
- Modifique as ações recomendadas para seu contexto
- Adicione ou remova seções conforme necessário

---

## 🎛️ Personalização

### Alterar intervalo de verificação

No nó **"A cada 5 minutos"**, altere o valor:

| Intervalo | Uso recomendado |
|-----------|-----------------|
| 1 min | APIs críticas, produção |
| 5 min | Monitoramento padrão |
| 15 min | Ambientes de staging |

### Alterar thresholds

No nó **"Avaliar Resposta"**, edite o código JavaScript:

```javascript
// Tempo máximo de resposta (padrão: 2000ms)
const isFast = responseTime < 3000;

// Status codes aceitos (padrão: 200-399)
const isUp = statusCode === 200; // aceitar apenas 200
```

### Adicionar retry (evitar falsos positivos)

Para evitar alertas por instabilidades momentâneas:

1. Após o nó **"Saudável?"** (caminho false), adicione um nó **Wait** (30 segundos)
2. Adicione um novo **HTTP Request** para a mesma URL
3. Se falhar novamente, então segue para o alerta

### Monitorar múltiplos endpoints

Duplique o workflow para cada endpoint, ou adicione nós HTTP Request paralelos conectados ao mesmo trigger.

---

## 📁 Estrutura do Repositório

```
n8n-synthetic-monitor/
├── workflows/
│   └── n8n-synthetic-monitor.json   # Workflow n8n pronto para importar
├── templates/
│   └── alerta-email.html            # Template HTML do alerta por email
├── docs/
│   └── images/                      # Screenshots e imagens do projeto
│       ├── banner.png               # Banner do README
│       └── email-preview.png        # Preview do template de email
├── .github/
│   └── ISSUE_TEMPLATE.md            # Template para issues
├── .gitignore
├── LICENSE                          # Licença MIT
├── CONTRIBUTING.md                  # Guia de contribuição
└── README.md                        # Este arquivo
```

---

## 🤝 Contribuindo

Contribuições são muito bem-vindas! Veja o [CONTRIBUTING.md](CONTRIBUTING.md) para detalhes.

**Formas de contribuir:**

- 🐛 Reportar bugs
- 💡 Sugerir novos recursos
- 📝 Melhorar documentação
- 🔧 Enviar pull requests

---

## 📋 Roadmap

- [x] Workflow base com validação tripla
- [x] Template HTML de alerta por email
- [x] Suporte a Slack, Email e Webhook
- [ ] Template de alerta para Slack (Block Kit)
- [ ] Dashboard de histórico de uptime
- [ ] Suporte a múltiplos endpoints no mesmo workflow
- [ ] Integração com Grafana para métricas de disponibilidade
- [ ] Alerta de recuperação (quando a API volta)

---

## 📄 Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

---

## 🙏 Agradecimentos

- [n8n](https://n8n.io/) — plataforma de automação
- [Grafana](https://grafana.com/) — visualização de métricas
- [K6](https://k6.io/) — testes de carga (projeto irmão)

---

<p align="center">
  <sub>Feito com ☕ por <a href="https://github.com/alerodriguesdesouza">Alessandro Rodrigues de Souza</a></sub>
</p>
