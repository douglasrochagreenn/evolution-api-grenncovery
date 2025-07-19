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
docker compose -f docker-compose.dev.yaml up -d --build
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
docker compose -f docker-compose.dev.yaml ps
```

```bash
docker logs -f evolution_api
```

```bash
curl -i http://localhost:8080/health
```

## Acessar o Manager UI

Abra no navegador: `http://localhost:8080/manager`

* **Server URL**: `http://localhost:8080`
* **API Key Global**: cole o valor de `AUTHENTICATION_API_KEY`

Clique em **Instance +** para criar e conectar uma instância de WhatsApp.

## Enviar mensagem via API

1. Liste instâncias para obter o `instanceId`:

   ```bash
   curl -s http://localhost:8080/instances \
     -H "x-api-key: $AUTHENTICATION_API_KEY" | jq
   ```
2. Formate o destino (ex.: `5511987654321@c.us`).
3. Envie a mensagem:

   ```bash
   curl -X POST http://localhost:8080/instances/<INSTANCE_ID>/send-message \
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
docker compose -f docker-compose.dev.yaml down --volumes --remove-orphans
````

## Troubleshooting

* Se aparecer erro `@prisma/client did not initialize`, garanta q
