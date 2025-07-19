# Evolution API (Docker)

Este documento descreve o que você precisa e como rodar a Evolution API usando Docker e Docker Compose.

## Pré-requisitos

* **Docker** (versão ≥ 20.10)
* **Docker Compose** (versão ≥ 1.29)
* **Git** (para clonar este repositório)

## Clonar o repositório

```bash
git clone <url-do-seu-repo> evolution-api
cd evolution-api
```

## Configurar variáveis de ambiente

Na raiz do projeto, crie um arquivo `.env` (ou copie o modelo `.env.example` se disponível) com as seguintes chaves mínimas:

```dotenv
DATABASE_PROVIDER=postgresql
DATABASE_CONNECTION_URI=postgresql://user:pass@postgres:5432/evolution?schema=public
AUTHENTICATION_API_KEY=minha-chave-secreta-aqui
CACHE_REDIS_ENABLED=true
CACHE_REDIS_URI=redis://redis:6379/6
CACHE_LOCAL_ENABLED=false
```

> **Importante:** os hostnames `postgres` e `redis` devem corresponder aos serviços no `docker-compose.yml`.

## Subir os containers

```bash
docker compose -f docker-compose.yaml up -d --build
```

Este comando irá:

* Buildar a imagem da API a partir do Dockerfile local
* Iniciar containers para:

  * `postgres` (PostgreSQL)
  * `redis` (Redis)
  * `evolution_api` (sua API)

## Verificar serviços

Para checar se está tudo funcionando:

```bash
docker logs -f evolution_api
```

```bash
curl -i {{ seu_link }}/health
```

## Acessar o Manager UI

Abra no navegador: `{{ seu_link }}/manager`

* **Server URL**: `{{ seu_link }}`
* **API Key Global**: cole o valor de `AUTHENTICATION_API_KEY`

Clique em **Instance +** para criar e conectar uma instância de WhatsApp.

## Enviar mensagem via API

1. Liste instâncias para obter o `instanceId`:

   ```bash
   curl -s {{ seu_link }}/instances \
     -H "x-api-key: $AUTHENTICATION_API_KEY" | jq
   ```
2. Formate o destino (ex.: `5511987654321@c.us`).
3. Envie a mensagem:

   ```bash
   curl -X POST {{ seu_link }}/instances/<INSTANCE_ID>/send-message \
     -H "Content-Type: application/json" \
     -H "x-api-key: $AUTHENTICATION_API_KEY" \
     -d '{"to":"5511987654321@c.us","message":"Olá! Esta é uma mensagem de teste."}'
   ```
4. Verifique a resposta JSON com status `SENT`.
5. (Opcional) Confira logs:

   ```bash
   ```

docker logs -f evolution\_api

````

## Parar e limpar tudo

```bash
docker compose -f docker-compose.yaml down --volumes --remove-orphans
````

## Troubleshooting

* Se aparecer erro `@prisma/client did not initialize`, garanta que o Dockerfile roda `prisma generate` antes de iniciar ou adicione `prestart` no `package.json`.
* Se `Redis disconnected`, verifique `CACHE_REDIS_URI` aponta para `redis://redis:6379`, não para `{{ seu_link }}`.
* Se tiver permissão no Postgres, use `POSTGRES_HOST_AUTH_METHOD=trust` em dev.
* Em produção, defina `AUTHENTICATION_API_KEY` com uma string forte.

---

Agora sua Evolution API deve estar funcionando plenamente via Docker, incluindo o envio de mensagens pelo WhatsApp!
