# 🚀 Let Me Ask - Plataforma de Q&A com IA

Bem-vindo ao repositório do **Let Me Ask**! Este projeto é uma plataforma de perguntas e respostas (Q&A) moderna, totalmente containerizada com Docker e orquestrada com Docker Compose. A aplicação utiliza a API do Gemini para gerar respostas inteligentes e o pgvector para realizar buscas semânticas, oferecendo uma experiência de usuário rica e interativa.

## ✨ Funcionalidades Principais

-   **Backend**: Uma API de serviço que gerencia a lógica de negócios.
-   **Frontend**: Uma aplicação web moderna para interação do usuário.
-   **Inteligência Artificial**: Integração com a API do Google Gemini para respostas inteligentes.
-   **Busca Semântica**: Utiliza PostgreSQL com a extensão `pgvector` para encontrar as perguntas mais relevantes.
-   **Cache de Alta Performance**: Redis para caching de dados e melhoria de performance.
-   **Segurança com HTTPS**: Configuração automática de certificados SSL/TLS com Nginx e Certbot.
-   **Ambiente Isolado**: Todos os serviços rodam em uma rede Docker dedicada.

---

## 🛠️ Tecnologias Utilizadas

| Ícone | Tecnologia | Descrição |
| :---: | :--- | :--- |
| 🐳 | **Docker & Docker Compose** | Containerização e orquestração dos serviços. |
| 🐘 | **PostgreSQL (com pgvector)** | Banco de dados relacional com suporte a vetores para busca semântica. |
| ⚡ | **Redis** | Banco de dados em memória para caching rápido. |
| 🤖 | **Google Gemini** | API de Inteligência Artificial para geração de conteúdo. |
| 🌐 | **Nginx** | Servidor web e proxy reverso para gerenciar o tráfego e servir certificados SSL. |
| 🔒 | **Certbot & Let's Encrypt** | Geração e renovação automática de certificados SSL/TLS gratuitos. |

---

## ⚙️ Configuração do Ambiente

Antes de iniciar, você precisa configurar as variáveis de ambiente.

1.  **Copie o arquivo de exemplo:**
    ```bash
    cp .env.example .env
    ```

2.  **Edite o arquivo `.env`** e preencha com seus próprios valores:

    ```dotenv
    # .env

    # Chave da API do Google Gemini
    # Obtenha a sua em https://aistudio.google.com/app/apikey
    GEMINI_API_KEY=SUA_CHAVE_API_DO_GEMINI

    # URL base da API que será usada pelo frontend
    # Deve apontar para o seu domínio da API (ex: https://api-let-me-ask.faraujo.com)
    VITE_API_BASE_URL=https://api-let-me-ask.faraujo.com
    ```

3.  **Atualize os Domínios:**

    > ⚠️ **IMPORTANTE:** Você **PRECISA** substituir os domínios de exemplo (`let-me-ask.faraujo.com` e `api-let-me-ask.faraujo.com`) pelos seus próprios domínios nos seguintes arquivos:
    >
    > -   `docker-compose.yml`: Nos comandos dos serviços `certbot-web` e `certbot-api`.
    > -   `nginx/conf.d/default.conf` (ou arquivo de configuração similar): Nos blocos `server_name`.

---

## 🚀 Como Executar o Projeto

**Pré-requisitos:**
*   [Docker](https://docs.docker.com/get-docker/)
*   [Docker Compose](https://docs.docker.com/compose/install/)

Siga os passos abaixo para colocar a aplicação no ar:

1.  **Clone o repositório:**
    ```bash
    git clone https://github.com/TalisonF/let-me-ask-server
    cd let-me-ask-server
    ```

2.  **Configure as variáveis de ambiente** e os domínios conforme a seção "Configuração" acima.

3.  **Obtenha os certificados SSL iniciais (apenas na primeira vez):**
    Descomente a linha `command` que contém `certonly` nos serviços `certbot-web` e `certbot-api` no `docker-compose.yml` e comente a linha de renovação. Em seguida, execute:
    ```bash
    docker-compose up nginx
    docker-compose up certbot-web
    docker-compose up certbot-api
    ```
    Após a geração bem-sucedida, reverta as alterações (comente a linha `certonly` e descomente a de renovação).

4.  **Inicie todos os serviços em segundo plano:**
    ```bash
    docker-compose up -d
    ```

5.  **Acesse a aplicação:**
    *   Frontend: `https://let-me-ask.faraujo.com`
    *   Backend: `https://api-let-me-ask.faraujo.com`

---

## 📦 Visão Geral dos Serviços (Containers)

| Serviço | Descrição |
| :--- | :--- |
| `nginx` | Atua como **Proxy Reverso**. Recebe todas as requisições nas portas 80/443, distribui o tráfego para o frontend (`webapp`) ou para o backend (`service`) e serve os certificados SSL para garantir a conexão HTTPS. |
| `let-me-ask-service` | O **coração da aplicação**. É a API backend responsável por toda a lógica de negócios, comunicação com os bancos de dados, cache e com a API externa do Gemini. |
| `let-me-ask-webapp` | O **Frontend da aplicação**. Interface com a qual o usuário interage, construída provavelmente com um framework moderno como React ou Vue (inferido pelo uso do Vite). |
| `let-me-ask-db` | O **banco de dados principal**. Utiliza PostgreSQL com a extensão `pgvector`, que permite o armazenamento e a consulta eficiente de vetores de embeddings, essencial para a funcionalidade de busca semântica. |
| `let-me-ask-redis` | Um **armazenamento de dados em memória**. É utilizado como cache para acelerar respostas e reduzir a carga no banco de dados principal. |
| `certbot-web` / `certbot-api` | Serviços responsáveis pela **segurança HTTPS**. Eles gerenciam a obtenção e a renovação automática de certificados SSL da Let's Encrypt para os domínios do frontend e do backend, respectivamente. |

---
