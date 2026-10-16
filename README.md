# Todo API

API REST de tarefas construída com Node.js e Express. O Node.js roda dentro do container Docker; não é necessário instalá-lo na máquina host.

## Pré-requisitos

- Docker instalado e em execução.

## Construir a imagem

Na raiz do repositório, onde está o arquivo `DOCKERFILE`, execute:

```bash
docker build -t todo-api -f DOCKERFILE .
```

O ponto final indica que a raiz do repositório é o contexto de build. Na primeira execução, o Docker baixa a imagem base do Node.js definida no `DOCKERFILE`.

## Iniciar a API

```bash
docker run --rm --name todo-api -p 3000:3000 todo-api
```

A API ficará disponível em `http://localhost:3000`. O container roda em primeiro plano; pressione `Ctrl+C` para encerrá-lo. Nesse caso, o Docker pode mostrar uma mensagem de interrupção do processo, que é esperada.

Para iniciar em segundo plano:

```bash
docker run -d --rm --name todo-api -p 3000:3000 todo-api
```

Para parar a execução em segundo plano:

```bash
docker stop todo-api
```

## Endpoints

- `GET /health` - verifica se a API está ativa.
- `GET /api/todos` - lista as tarefas.
- `GET /api/todos/:id` - consulta uma tarefa pelo ID.
- `POST /api/todos` - cria uma tarefa.
- `PUT /api/todos/:id` - atualiza uma tarefa.
- `DELETE /api/todos/:id` - remove uma tarefa.
- `GET /api/stats` - retorna estatísticas das tarefas.

Exemplo para verificar a API:

```bash
curl http://localhost:3000/health
```

Exemplo para criar uma tarefa:

```bash
curl -X POST http://localhost:3000/api/todos \
  -H 'Content-Type: application/json' \
  -d '{"title":"Estudar Docker","description":"Executar a API em um container"}'
```

Os dados das tarefas ficam em memória e são reiniciados quando o container é encerrado.
