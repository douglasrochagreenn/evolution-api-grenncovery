Evolution API (Docker)

Este documento descreve o que você precisa e como rodar a Evolution API usando Docker e Docker Compose.

Pré-requisitos

Docker (versão ≥ 20.10)

Docker Compose (versão ≥ 1.29)

Git (para clonar este repositório)

1. Clonar o repositório

git clone <url-do-seu-repo> evolution-api
cd evolution-api

2. Configurar variáveis de ambiente

Na raiz do projeto, crie um arquivo .env (ou copie o modelo .env.example se disponível) com as seguintes chaves mínimas:

### Tipo de banco de dados suportado: postgresql ou mysql
DATABASE_PROVIDER=postgresql

### String de conexão ao banco Postgres (serviço definido no Compose como "postgres")
DATABASE_CONNECTION_URI=postgresql://user:pass@postgres:5432/evolution?schema=public

### Chave de API global para o Manager UI e chamadas HTTP
AUTHENTICATION_API_KEY=minha-chave-secreta-aqui

### Redis (cache) - serviço definido no Compose como "redis"
CACHE_REDIS_ENABLED=true
CACHE_REDIS_URI=redis://redis:6379/6
CACHE_LOCAL_ENABLED=false

### Outras variáveis (opcionais) podem ser mantidas nos valores padrão

Importante: o hostname postgres e redis devem corresponder aos nomes dos serviços no seu docker-compose.yml.

3. Subir os containers

Use Docker Compose para buildar e iniciar os serviços:

docker compose -f docker-compose.dev.yaml up -d --build

Isso irá:

Buildar a imagem da API a partir do Dockerfile local

Iniciar containers para:

postgres (PostgreSQL)

redis (Redis)

evolution_api (sua API)

4. Verificar se está tudo rodando

Status dos containers:

docker compose -f docker-compose.dev.yaml ps

Logs da API:

docker logs -f evolution_api

Verifique se não há erros de conexão ao Redis ou ao Postgres.

Testar endpoint de health:

curl -i http://localhost:8080/health

Deve retornar 200 OK ou JSON indicando que a API está saudável.

5. Acessar o Manager UI

Abra no navegador:

http://localhost:8080/manager

Insira em Server URL: http://localhost:8080

Parar e limpar tudo

Para parar e remover containers, redes e volumes associados:

docker compose -f docker-compose.dev.yaml down --volumes --remove-orphans

7. Troubleshooting / Dicas

Prisma: se der erro @prisma/client did not initialize, verifique se o Dockerfile roda prisma generate antes de iniciar ou inclua um script prestart no package.json.

Redis disconnected: confira CACHE_REDIS_URI apontando para redis://redis:6379, não para localhost.

Postgres permissions: use POSTGRES_HOST_AUTH_METHOD=trust no serviço Postgres para desenvolvimento.

Atualize o AUTHENTICATION_API_KEY para uma string forte em produção.Em API Key Global, cole o valor de AUTHENTICATION_API_KEY do seu .env.

Após o login, clique em Instance + para criar e conectar uma instância de WhatsApp.

6. Parar e limpar tudo

Para parar e remover containers, redes e volumes associados:

docker compose -f docker-compose.dev.yaml down --volumes --remove-orphans

7. Troubleshooting / Dicas

Prisma: se der erro @prisma/client did not initialize, verifique se o Dockerfile roda prisma generate antes de iniciar ou inclua um script prestart no package.json.

Redis disconnected: confira CACHE_REDIS_URI apontando para redis://redis:6379, não para localhost.

Postgres permissions: use POSTGRES_HOST_AUTH_METHOD=trust no serviço Postgres para desenvolvimento.

Atualize o AUTHENTICATION_API_KEY para uma string forte em produção.

