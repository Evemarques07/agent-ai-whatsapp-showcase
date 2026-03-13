<div align="center">

# 🤖 Agent AI WhatsApp

### Agente multimodal inteligente para WhatsApp com processamento de texto, áudio, imagem e PDF

![Python](https://img.shields.io/badge/Python_3.12-3776AB?style=for-the-badge&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain-1C3C3C?style=for-the-badge&logo=langchain&logoColor=white)
![Gemini](https://img.shields.io/badge/Google_Gemini-8E75B2?style=for-the-badge&logo=googlegemini&logoColor=white)
![WhatsApp](https://img.shields.io/badge/WhatsApp_API-25D366?style=for-the-badge&logo=whatsapp&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)

</div>

---

## 📋 Sobre o Projeto

**Agent AI WhatsApp** é um agente inteligente que opera via WhatsApp, capaz de processar **texto, áudio, imagens e PDFs**. Construído com **FastAPI + LangChain + Gemini**, utiliza **Chain of Thought (CoT)** para raciocínio em 6 etapas, consulta banco de dados MySQL em tempo real e escala automaticamente para atendentes humanos quando necessário.

Suporta **múltiplos providers de LLM** (Gemini, OpenAI, Anthropic, Groq, Ollama, DeepSeek) e é **treinável para qualquer ramo de negócio** via prompt engineering.

> ⚠️ Este repositório é uma vitrine do projeto. O código-fonte é mantido em repositório privado.

---

## 🏗️ Arquitetura

```
┌─────────────────┐         ┌─────────────────────────────────┐
│   📱 Cliente    │         │         FastAPI Server           │
│   WhatsApp      │         │                                 │
│                 │  Webhook │  ┌───────────┐  ┌───────────┐  │
│  Texto ────────►├────────►│  │  WuzAPI   │  │  Memory   │  │
│  Áudio ────────►│         │  │  Router   │  │  Manager  │  │
│  Imagem ───────►│         │  └─────┬─────┘  └───────────┘  │
│  PDF ──────────►│         │        │                        │
│                 │         │  ┌─────▼─────────────────────┐  │
│                 │         │  │     File Processor        │  │
│                 │         │  │ • Audio → Transcrição     │  │
│                 │         │  │ • Image → Compress/Tile   │  │
│                 │         │  │ • PDF → JPEG pages        │  │
│                 │         │  └─────┬─────────────────────┘  │
│                 │         │        │                        │
│                 │         │  ┌─────▼─────────────────────┐  │
│                 │         │  │     LangChain Agent       │  │
│                 │         │  │  Chain of Thought (6 steps)│  │
│                 │         │  │  System Prompt (~11KB)    │  │
│                 │         │  └──┬──────────────────┬─────┘  │
│                 │         │     │                  │        │
│                 │         │  ┌──▼──────┐   ┌──────▼─────┐  │
│  ◄──────────────┤◄────────│  │ Gemini  │   │  MySQL DB  │  │
│   Resposta      │         │  │ LLM     │   │  Consultas │  │
│                 │         │  └─────────┘   └────────────┘  │
└─────────────────┘         └─────────────────────────────────┘
```

---

## 🚀 Funcionalidades

### 💬 Processamento de Texto
- Compreensão de linguagem natural com LangChain
- **Chain of Thought** em 6 etapas (raciocínio interno oculto do cliente)
- Respostas contextualizadas ao ramo do negócio
- Formatação automática para WhatsApp (`*bold*`, `_italic_`)

### 🎙️ Processamento de Áudio
- Transcrição automática via **Gemini Whisper**
- Suporte: WAV, MP3, OGG, FLAC, M4A, AAC (até 20MB)
- Tag interna `[TRANSCRIÇÃO]` removida antes do envio
- Supervisor recebe transcrição completa em escalações

### 🖼️ Processamento de Imagem
- Análise visual via **Gemini Vision**
- Compressão automática (JPEG quality 85) para imagens 20-52MB
- **Tiling** para imagens >4096px (até 9 tiles)
- Formatos: JPEG, PNG, GIF, WebP (até 52MB)

### 📄 Processamento de PDF
- Conversão de páginas para JPEG (150 DPI) via **PyMuPDF**
- Análise visual de cada página
- Até 50 páginas / 52MB por documento

### 🗄️ Consulta ao Banco de Dados (Real-time)
- Busca de cliente por **CPF** ou **nome** (fuzzy search)
- Informações retornadas: vendedor responsável, próxima visita, último acerto
- Conexão assíncrona via **aiomysql** (pool de 1-5 conexões)

### 🔄 Sistema de Escalação
- **Triggers automáticos**:
  - Cliente analfabeto
  - Solicitação explícita de atendente humano
  - Problemas financeiros (Serasa, SPC, inadimplência)
  - Situações desconhecidas ou reclamações graves
- Notificação ao supervisor com contexto da conversa
- Tag interna `[ESCALAR]` processada pelo sistema

### 🧠 Memória de Conversa
- Thread por número de telefone (thread_id = phone)
- Estratégias: Buffer, Window, Summary
- Limite de tokens configurável (padrão: 16.000)
- Estimativa: ~4 caracteres por token

---

## 🤖 Multi-Provider LLM

| Provider | Modelos |
|----------|---------|
| **Google Gemini** | gemini-3-flash-preview (padrão) |
| **OpenAI** | GPT-4, GPT-3.5 |
| **Anthropic** | Claude |
| **Groq** | LLaMA, Mixtral |
| **Ollama** | Modelos locais |
| **OpenRouter** | Múltiplos |
| **DeepSeek** | DeepSeek |
| **xAI** | Grok |
| **AWS Bedrock** | Claude, Titan |
| **Azure** | OpenAI models |

---

## ⚡ Tags Internas (auto-removidas)

| Tag | Função |
|-----|--------|
| `<pensamento>...</pensamento>` | Chain of Thought (raciocínio oculto) |
| `[TRANSCRIÇÃO: ...]` | Texto do áudio transcrito |
| `[BUSCA_BANCO]` | Resultado de consulta ao DB |
| `[ESCALAR]` | Trigger de escalação |

---

## 🐳 Infraestrutura

```yaml
services:
  fm-agent:   # FastAPI · Python 3.12 · Port 8000
  wuzapi:     # WhatsApp Integration · Port 8080

networks:
  fm-network:           # Bridge interno
  backend-fm_default:   # Acesso ao MySQL compartilhado
```

---

## 📡 API Endpoints

| Endpoint | Método | Função |
|----------|--------|--------|
| `/` | GET | Informações da API |
| `/health` | GET | Health check |
| `/ready` | GET | Readiness (inclui status do DB) |
| `/webhook` | GET | Verificação WuzAPI |
| `/webhook` | POST | Recepção de mensagens WhatsApp |

---

## 🛠️ Stack Completa

| Camada | Tecnologia |
|--------|-----------|
| **Runtime** | Python 3.12 |
| **Framework** | FastAPI · Uvicorn (ASGI) |
| **AI/LLM** | LangChain · Google Gemini (padrão) · 10 providers |
| **Áudio** | Gemini Whisper (transcrição) |
| **Imagem** | Gemini Vision · Pillow (compress/tile) |
| **PDF** | PyMuPDF (página → JPEG) |
| **Database** | MySQL · aiomysql (async pool) |
| **WhatsApp** | WuzAPI (webhooks + envio) |
| **Validação** | Pydantic v2 · pydantic-settings |
| **Logging** | structlog (JSON) |
| **Retry** | tenacity |
| **Serialização** | orjson |
| **Infra** | Docker Compose · TZ America/Fortaleza |

---

## 🎯 Diferenciais

- **Multimodal** — texto, áudio, imagem e PDF em um único agente
- **Chain of Thought** — raciocínio em 6 etapas para respostas precisas
- **Treinável** — system prompt de ~11KB customizável por ramo
- **Escalação inteligente** — sabe quando transferir para humano
- **Consulta DB em tempo real** — busca dados do cliente durante a conversa
- **Multi-provider** — troque de LLM sem alterar código
- **Autônomo** — atende múltiplos clientes simultaneamente 24/7

---

## 👨‍💻 Autor

Desenvolvido por [Everton Marques](https://github.com/Evemarques07)

---

<div align="center">

*Projeto privado — código-fonte não disponível para clone.*

</div>
