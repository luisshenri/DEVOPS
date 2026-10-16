# Todo API

API REST de tarefas construída com Node.js e Express. O Node.js roda dentro do container Docker; não é necessário instalá-lo na máquina host.

## Objetivo do repositório

Este repositório é um ambiente de estudo para testar e comparar diferentes pipelines de CI/CD com GitHub Actions. A API Todo serve como aplicação de exemplo para validar etapas de instalação, testes, lint, build e a passagem de artefatos entre jobs. Os deploys dos workflows são demonstrativos: não publicam a aplicação em um servidor ou serviço de hospedagem.

## Workflows de CI/CD

Os workflows ficam em `.github/workflows/` e demonstram três abordagens:

| Workflow | Abordagem | Quando usar | Vantagens | Trade-offs |
| --- | --- | --- | --- | --- |
| `ci-matrix.yml` | Matriz de versões do Node.js (18, 20 e 22) e sistemas operacionais (Ubuntu, Windows e macOS) | Quando é importante verificar compatibilidade em diferentes ambientes | Ampla cobertura e identificação antecipada de diferenças entre versões e sistemas | Executa até nove combinações, aumentando tempo de execução e consumo de minutos do GitHub Actions |
| `ci-multistage.yml` | Estágios de validação, build e deploy, com testes, lint e auditoria em paralelo antes do build | Quando etapas posteriores só devem acontecer após as validações anteriores | Fluxo explícito, falhas detectadas antes do build e artefato passado entre jobs | Mais configuração e tempo total; jobs dependentes aguardam os anteriores. A auditoria está configurada para não bloquear a pipeline (`|| true`) |
| `ci-reusable.yml` e `reusable-test.yml` | Workflow de testes reutilizável chamado para Node.js 18 e 20 | Quando a mesma rotina deve ser compartilhada por vários workflows ou versões | Evita duplicação e centraliza a manutenção da rotina de testes | Exige definir entradas e dependências do workflow chamado; alterações nele afetam todos os chamadores |

O workflow multistage e os workflows reutilizáveis incluem uma etapa chamada deploy, mas ela apenas imprime mensagens no log. Não há publicação real da API.

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
