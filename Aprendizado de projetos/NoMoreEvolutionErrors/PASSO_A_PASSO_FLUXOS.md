# Guia: Criando Novos Fluxos e Bancos em Docker (Evitando Erros)

Este guia explica como estruturar novos projetos de IA e automação sem cair em erros de conexão como o **P1001** (Database unreachable).

---

## 1. O Conceito Fundamental: O "Cérebro" do Docker

No seu computador, se você abrir um navegador e digitar `localhost`, ele acessa o próprio computador. No Docker, **cada serviço (container) é como se fosse um computador diferente.**

- **O que deu errado:** Quando o `evolution_api` tentava acessar `localhost:5432`, ele estava procurando o banco **dentro dele mesmo**, onde não havia nada.
- **A regra de ouro:** Dentro do Docker, **nunca use `localhost`** para conectar um serviço a outro. Use sempre o **nome do serviço** definido no `docker-compose.yml`.

---

## 2. Passo a Passo para um Novo Fluxo (Banco + API)

Sempre que quiser criar um novo ambiente ou adicionar um novo banco para um fluxo de IA:

### Passo 1: Definir no `docker-compose.yml`

Cadastre o serviço e o banco. Dê nomes claros.

```yaml
services:
  meu-novo-banco: # <--- Este será o HOST no seu .env
    image: postgres:15
    environment:
      - POSTGRES_USER=usuario
      - POSTGRES_PASSWORD=senha
      - POSTGRES_DB=nome_do_banco
    # ... healthchecks e volumes
```

### Passo 2: Configurar o `.env`

Use o nome que você deu no Passo 1.

```env
# CORRETO (Dentro do Docker):
DATABASE_URL=postgresql://usuario:senha@meu-novo-banco:5432/nome_do_banco

# ERRADO (Vai dar erro P1001):
DATABASE_URL=postgresql://usuario:senha@localhost:5432/nome_do_banco
```

### Passo 3: Ordem de Inicialização (Essential!)

Bancos de dados levam tempo para "acordar". Se a sua API subir antes do banco estar pronto, ela vai dar erro.
Sempre use o `healthcheck` e `depends_on` como fizemos no seu arquivo agora:

```yaml
sua-api:
  depends_on:
    meu-novo-banco:
      condition: service_healthy # Espera o banco dizer "estou pronto!"
```

---

## 3. Como Resetar um Banco sem Erros

Se você quiser "limpar" tudo e começar um banco do zero para um novo fluxo:

1. **Pare tudo e limpe os volumes de dados:**

   ```bash
   docker compose down -v
   ```

   _O `-v` apaga as pastas internas do Docker onde o banco guarda as tabelas._

2. **Apague manualmente pastas de dados mapeadas (se houver):**
   Se você usa `./data` ou algo parecido no seu PC, apague o conteúdo da pasta manualmente.

3. **Inicie do zero:**
   ```bash
   docker compose up -d
   ```

---

## 4. Dicas para Fluxos de IA (Melhores Práticas)

Para seus futuros fluxos de IA (LangChain, Flowise, Typebot, etc.):

1. **Use Redes Isoladas:** Sempre mantenha seus fluxos em uma `network` comum no Docker Compose para que eles se "vejam" sem precisar expor portas para a internet.
2. **Volumes para Logs:** APIs de IA geram muitos logs. Sempre mapeie uma pasta de logs:
   ```yaml
   volumes:
     - ./logs/meu-fluxo:/app/logs
   ```
3. **Variáveis de API Seguras:** Nunca coloque chaves da OpenAI ou Anthropic direto no `docker-compose.yml`. Use sempre o `.env` e adicione o `.env` ao `.gitignore` se for subir para o GitHub.

### Resumo Sugerido para Novos Projetos:

1. Copie o `docker-compose.yml` e o `.env` de um projeto que já funciona.
2. Altere os nomes dos serviços e as senhas.
3. Verifique se as URLs de conexão estão usando os nomes dos serviços.
4. Rode `docker compose up -d` e verifique os logs com `docker compose logs -f`.

Se o erro **P1001** aparecer: **Verifique se o nome do host na URL do banco é exatamente o mesmo nome que está escrito no serviço do Postgres no seu YAML.**

---

## 5. Modelo Final Completo (Para Copiar e Colar)

Aqui está como deve ser a estrutura final ideal para um projeto de automação com **n8n + Evolution API + Postgres + Redis**.

### 📄 Exemplo de `docker-compose.yml` completo:

```yaml
version: '3.9'

services:
  n8n:
    image: n8nio/n8n
    container_name: n8n
    ports:
      - '5678:5678'
    volumes:
      - ./data:/home/node/.n8n
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=seu_admin
      - N8N_BASIC_AUTH_PASSWORD=sua_senha
    restart: always
    networks:
      - minha_rede

  evolution-api:
    image: atendai/evolution-api:v2.1.1
    container_name: evolution_api
    ports:
      - '8080:8080'
    env_file:
      - .env
    volumes:
      - evolution_instances:/evolution/instances
    depends_on:
      postgres:
        condition: service_healthy # <--- Crucial!
      redis:
        condition: service_started
    restart: always
    networks:
      - minha_rede

  postgres:
    image: postgres:15
    container_name: evolution_postgres
    environment:
      - POSTGRES_USER=evolution
      - POSTGRES_PASSWORD=evolution
      - POSTGRES_DB=evolution
    ports:
      - '5432:5432'
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck: # <--- Diz para a API quando o banco está pronto
      test: ['CMD-SHELL', 'pg_isready -U evolution -d evolution']
      interval: 10s
      timeout: 5s
      retries: 5
    restart: always
    networks:
      - minha_rede

  redis:
    image: redis:alpine
    container_name: evolution_redis
    command: redis-server --appendonly yes
    restart: always
    networks:
      - minha_rede

volumes:
  evolution_instances:
  postgres_data:

networks:
  minha_rede:
    driver: bridge
```

## 6. O Combo de Ouro do Windows (O que o Senior ensinou)

Se você estiver rodando Docker no Windows Desktop, existem 3 ajustes que **evitam 99% dos erros** de conexão com o WhatsApp:

1.  **Túnel Público (Ngrok):** O WhatsApp precisa de uma URL segura (`https`) para validar a conexão. Se a sua API estiver apenas em `localhost`, ela não conseguirá "falar" com os servidores do Meta.
    - _Comando:_ `ngrok http 8080` (pegue a URL gerada e coloque no `SERVER_URL`).
2.  **DNS e IPv6:** O Windows às vezes se perde tentando resolver endereços via IPv6. No seu `docker-compose.yml`, sempre adicione:
    ```yaml
    dns:
      - 1.1.1.1
    sysctls:
      - net.ipv6.conf.all.disable_ipv6=1
    ```
3.  **Community Nodes no n8n:** Se a API Evolution estiver muito pesada ou difícil de configurar, você pode instalar o nó da comunidade `n8n-nodes-whatsapp-web` direto no n8n. Ele é gratuito e o QR Code aparece direto no seu fluxo!

---

## 7. Modelo Final Definitivo (O que FUNCIONOU hoje)

Use este modelo para seus projetos futuros no Windows. Ele já vem com os "segredos" de rede.

### 📄 `docker-compose.yml` (Windows Ready):

```yaml
services:
  postgres:
    image: postgres:15-alpine
    container_name: evolution_postgres
    environment:
      POSTGRES_USER: zenith_admin
      POSTGRES_PASSWORD: sua_senha_forte
      POSTGRES_DB: evolution_db
    volumes:
      - pg_data:/var/lib/postgresql/data
    ports:
      - '5432:5432'
    restart: always

  redis:
    image: redis:alpine
    container_name: evolution_redis
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    restart: always

  evolution:
    image: atendai/evolution-api:v2.1.1
    container_name: evolution_api
    security_opt:
      - seccomp:unconfined
    ports:
      - '8080:8080'
    depends_on:
      - postgres
      - redis
    dns:
      - 1.1.1.1
      - 8.8.8.8
    sysctls:
      - net.ipv6.conf.all.disable_ipv6=1
    environment:
      - SERVER_TYPE=http
      - SERVER_PORT=8080
      - SERVER_URL=https://SUA_URL_DO_NGROK.ngrok-free.app # <--- MUDAR AQUI
      - AUTHENTICATION_API_KEY=minha_api_123
      - DATABASE_ENABLED=true
      - DATABASE_PROVIDER=postgresql
      - DATABASE_CONNECTION_URI=postgresql://zenith_admin:sua_senha_forte@postgres:5432/evolution_db?schema=public
      - REDIS_ENABLED=true
      - REDIS_URI=redis://redis:6379/0
      - CACHE_REDIS_ENABLED=true
      - CACHE_REDIS_URI=redis://redis:6379/1
      - WEBSOCKET_ENABLED=true
      - WEBSOCKET_GLOBAL_WEB-QRCODE=true
    restart: always

  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    ports:
      - '5678:5678'
    environment:
      - N8N_COMMUNITY_PACKAGES_ENABLED=true # <--- Para instalar nós extras
    volumes:
      - n8n_data:/home/node/.n8n
    restart: always

volumes:
  pg_data:
  redis_data:
  n8n_data:
```

### 📄 Dica para o Futuro:

Sempre que o QR Code não aparecer no navegador:

1. Limpe o cache do Chrome (F12 -> Botão direito no Refresh -> Esvaziar Cache).
2. Verifique se o Ngrok ainda está ativo (ele expira se você não tiver conta paga, então tem que reiniciar o comando e atualizar o `SERVER_URL`).

---

## 🚨 CHECKLIST DE SEGURANÇA (Para evitar surtos)

Antes de rodar qualquer fluxo novo, verifique estes 5 pontos:

1.  **O Ngrok está ligado?** Se você reiniciou o PC, a URL do Ngrok mudou! Atualize o `SERVER_URL` no seu arquivo.
2.  **O IPv6 está desativado?** Garanta que as linhas de `sysctls` estão lá para evitar que a conexão caia.
3.  **Portas em conflito?** Se o Docker disser "Port already in use", feche outros programas que usem a porta 8080 ou 5432.
4.  **Nomes dos Serviços:** Se o seu Postgres no YAML se chama `evolution_postgres`, a sua URL de conexão **precisa** usar `@evolution_postgres` e não `@localhost`.
5.  **A Regra do Ouro do Reset:** Se nada estiver fazendo sentido, dê um `docker compose down -v` e suba tudo de novo. O `-v` é o seu botão de "Formatar" que resolve 90% dos problemas.
