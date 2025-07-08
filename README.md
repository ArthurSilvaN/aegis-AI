# Aegis-AI

Sistema de detecção e resposta a incidentes de segurança com dois microserviços baseados em agentes de IA.

## 👥 Equipe

- Arthur Rafael
- Caio Bastos
- Nicolas Lopes
- Pedro Rabelo

## 🎯 Objetivo do Projeto

Desenvolver um sistema distribuído utilizando dois agentes de Inteligência Artificial que cooperam para:

- Detectar anomalias em logs de rede e sistema (IA 1 - Sensor)
- Classificar eventos como incidentes ou falsos positivos (IA 2 - Analista)
- Registrar e disponibilizar os eventos analisados via API REST

---

## Arquitetura

*   **Agente Local (Ollama)**: Analisa logs brutos e gera um resumo técnico.
*   **Agente Online (Gemini)**: Recebe o resumo e gera recomendações de mitigação.

## Como executar

### 1. Agente Local

**Pré-requisitos:**

*   Python 3.10+
*   Ollama com o modelo `llama3` instalado e em execução.

**Instalação:**

```bash
cd agente_local
pip install -r requirements.txt
```

**Execução:**

```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```

### 2. Agente Online

**Pré-requisitos:**

*   Python 3.10+
*   Chave de API do Gemini configurada como variável de ambiente `GEMINI_API_KEY`.

**Instalação:**

```bash
cd agente_online
pip install -r requirements.txt
```

**Execução:**

```bash
uvicorn main:app --host 0.0.0.0 --port 8001
```

## Como executar com Docker Compose

**Pré-requisitos:**

*   Docker e Docker Compose instalados.
*   Chave de API do Gemini em um arquivo `.env` na raiz do projeto:

```
GEMINI_API_KEY=SUA_CHAVE_DE_API
```

**Execução:**

```bash
docker-compose up --build
```

## Como usar

Envie uma requisição POST para o `agente_local` com os logs a serem analisados:

```bash
curl -X POST "http://localhost:8000/analisar_logs/" -H "Content-Type: application/json" -d '{"logs": "...seus logs aqui..."}'
```

---

## 🧩 Componentes da Solução

| Componente      | Função                                                                     |
| --------------- | -------------------------------------------------------------------------- |
| IA 1 – Sensor   | Detecta anomalias com modelos locais (PyOD, Isolation Forest)              |
| IA 2 – Analista | Classifica o incidente com base em técnicas supervisionadas (scikit-learn) |
| RabbitMQ / gRPC | Comunicação entre agentes                                                  |
| API REST        | Exposição dos incidentes analisados                                        |

---

## ⚙️ Tecnologias Utilizadas

- **Docker**: containerização dos agentes
- **Python**: desenvolvimento dos agentes e API
- **FastAPI**: criação da API REST
- **RabbitMQ** ou **gRPC**: comunicação entre agentes
- **PyOD**, **Scikit-learn**: detecção e classificação de anomalias

---

## 🔐 Documentação Arquitetônica

### 🔹 Visão Inicial (Pré-modelagem de ameaças)

#### Diagrama Simplificado

```
[ Fontes de Log ]
       ↓
  [ IA 1: Sensor ] ← Docker
       ↓ gRPC / MQ
  [ IA 2: Analista ]
       ↓
    [ API REST ]
```

#### Riscos

- Comunicação entre agentes sem autenticação
- Dados sensíveis sem criptografia
- API sem controle de acesso
- Possibilidade de spoofing/log injection

### 🔸 Visão Final (Pós-modelagem de ameaças)

| Medida                      | Justificativa                  |
| --------------------------- | ------------------------------ |
| TLS em gRPC/RabbitMQ        | Criptografia de ponta-a-ponta  |
| JWT entre agentes           | Autenticação e autorização     |
| Sanitização de entradas     | Prevenção contra log injection |
| API com autenticação        | Controle de acesso básico      |
| Logging assíncrono e seguro | Auditabilidade e desempenho    |

---

## ✅ Validação do Problema

### Relevância

Empresas lidam com grandes volumes de logs, mas a maioria dos ataques é detectada tardiamente por falta de análise contextual automatizada.

### Dor

- Sobrecarga de SOCs (centros de operação de segurança)
- Alto volume de falsos positivos
- Tempo médio de resposta elevado (MTTR)

Este projeto busca aplicar IA para reduzir o esforço humano e acelerar a triagem de incidentes.

---

## 📚 Referências

- IBM. *Cost of a Data Breach Report 2023*. Disponível em: [https://www.ibm.com/reports/data-breach](https://www.ibm.com/reports/data-breach)
- VERIZON. *Data Breach Investigations Report 2024*. Disponível em: [https://www.verizon.com/business/resources/reports/dbir/](https://www.verizon.com/business/resources/reports/dbir/)
- NIST. *Guide to Intrusion Detection and Prevention Systems (SP 800-94)*. 2007.
- OWASP. *Logging and Monitoring Failures*. [https://owasp.org/Top10/A09\_2021-Logging\_and\_Monitoring\_Failures/](https://owasp.org/Top10/A09_2021-Logging_and_Monitoring_Failures/)
- SCARFONE, K.; MELL, P. *Guide to Intrusion Detection and Prevention Systems*. NIST, 2007.

---

## 📦 Estrutura Esperada

```
📁 aegis-ai/
├── agente_local/
│   └── main.py
├── agente_online/
│   └── main.py
├── api/
│   └── main.py
├── docker-compose.yml
└── README.md
```

---

## 🚀 Execução (simulada)

```bash
# Subir os containers
$ docker-compose up --build

# Testar a API
GET http://localhost:8000/analisar_logs
```

---
