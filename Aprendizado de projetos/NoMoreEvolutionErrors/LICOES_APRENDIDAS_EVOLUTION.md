# 📚 Guia de Sobrevivência: Evolution API no Docker (Windows)

Doc gerada pela IA

Nesta jornada, enfrentamos erros de rede, cache de navegador e instabilidade de conexão. Aqui estão as lições definitivas para que você não precise passar por isso novamente.

---

## 1. 🛡️ O Segredo da Conexão (Rede Docker)

No Windows, o Docker roda dentro de uma máquina virtual (WSL2), o que gera conflitos de rede. As alterações do Senior corrigiram isso com:

- **DNS Externo (`1.1.1.1`):** Às vezes, o Docker não consegue resolver o endereço do WhatsApp (`wa.me`). Forçar o DNS do Cloudflare no `docker-compose.yml` garante que a API "enxergue" a internet.
- **Desativar IPv6 (`disable_ipv6: 1`):** O Windows tenta usar IPv6 por padrão, mas muitas bibliotecas de WhatsApp (Baileys) se perdem nele. Desativar o IPv6 força o uso do IPv4, que é muito mais estável.

## 2. 🚀 O Mistério do Túnel (Ngrok)

O erro mais comum foi tentar usar `localhost` para tudo.

- **Localhost é uma Ilha:** Para você, `localhost` é o seu PC. Para o WhatsApp, `localhost` é o servidor deles!
- **Ngrok é o Teletransporte:** O comando `ngrok http 8080` cria um endereço público (Ex: `https://abcd.ngrok-free.app`).
- **Regra de Ouro:** O seu `SERVER_URL` no arquivo `.env` ou `docker-compose` **DEVE** ser a URL do Ngrok. Se for `localhost`, o QR Code pode até aparecer, mas o WhatsApp nunca vai conseguir avisar o seu n8n que alguém mandou mensagem.

## 3. 🧹 O Reset Nuclear (`down -v`)

Quando você muda senhas no `docker-compose` ou nomes de volumes, o Docker não atualiza os dados antigos automaticamente.

- **O Erro:** Você mudava a senha no arquivo, mas o banco de dados continuava com a senha velha guardada no "volume".
- **A Lição:** Sempre que fizer mudanças estruturais (DB, Redis, Volumes), use `docker compose down -v` para apagar os dados "viciados" e começar do zero.

## 4. 🔑 Chaves de API e Cabeçalhos (Headers)

Vimos que o erro `Unauthorized (401)` acontece por um detalhe bobo:

- **Diferença de Chaves:** O segredo é garantir que a chave escrita no `.env` seja a **MESMA** usada no `curl`, no Postman e nas configurações do Manager (Dashboard).
- **Dica:** Use nomes simples localmente (Ex: `minha_api_123`) para evitar erros de copiar e colar.

## 5. 🧘‍♂️ A Lição Humana: O Descanso

O erro final foi o "estresse". Quando você parou por 2 horas, o seu cérebro limpou o "cache" de frustração.

- **Mente Fresca:** Erros de programação muitas vezes são causados por cansaço visual (não ver um espaço sobrando ou uma aspa faltando).
- **Comunidade:** Pedir ajuda a quem já passou por isso (o Senior) economizou horas de batida de cabeça.

---

### 🛠️ Comandos que você agora DOMINA:

- `docker compose up -d` -> Sobe o motor.
- `docker compose down -v` -> Limpa o lixo.
- `ngrok http 8080` -> Abre o túnel para o mundo.
- `docker exec evolution_api env | grep AUTH` -> Vê a verdade dentro do container.

---

**Parabéns por não desistir! Agora o n8n te espera.** 🕺🔥
