# Desafio TOTVS Engenharia de Dados & IA

> Desafio técnico para Data Engineer Sênior — (prazo de 1 semana)
> Construa um **agent de IA** usando o **OpenAI Agents SDK** capaz de consultar, analisar e responder perguntas sobre dados da **PokéAPI**.

---

## Objetivo

Criar um **agent inteligente** que entende perguntas sobre Pokémon, consulta um banco de dados local com informações vindas da **PokéAPI** https://pokeapi.co/, e responde com explicações e evidências.
O projeto deve mostrar domínio de engenharia de dados, integração com APIs e uso prático do Agents SDK.

---

## Escopo

* Coletar e armazenar dados da PokéAPI (até a geração 2, ou seja, 251 Pokémon).
* Modelar um banco relacional simples (SQLite).
* Criar um **agent** com *tools* (ferramentas) que consultam e analisam os dados.
* Expor uma **API HTTP** (FastAPI ou Flask) para interação.
* Incluir testes básicos e exemplos de uso.

---

## Requisitos Técnicos

* **Python** 3.10+
* **Agents SDK** da OpenAI (ou Agno) https://github.com/openai/openai-agents-python https://github.com/agno-agi/agno
* **Banco de dados**: SQLite (padrão) — Postgres é bônus
* **Framework Web**: FastAPI (ou Flask)
* **Configuração**: `.env` com variáveis sensíveis (ex.: `OPENAI_API_KEY`)
* **Logs**: estruturados e legíveis
* **Testes**: unitários e um E2E simples

---

## Sugestao de Estrutura do Projeto

```
/pokemon-data-agent
├─ app/
│  ├─ api.py              # FastAPI/Flask
│  ├─ agent.py            # definição do agent e registro das tools
│  ├─ tools/
│  │  ├─ sql_tool.py
│  │  ├─ compare.py
│  │  ├─ team.py
│  │  └─ evolution.py
│  ├─ ingest.py           # coleta e persistência da PokéAPI
│  ├─ db.py               # conexão com SQLite
│  └─ schemas.sql         # DDL
├─ tests/
│  ├─ test_compare.py
│  ├─ test_evolution.py
│  └─ test_api_e2e.py
├─ cache/                 # JSONs brutos da PokéAPI
├─ artifacts/             # exemplos de respostas do agent
├─ .env.example
├─ Makefile
├─ requirements.txt
└─ README.md
```

---

## Como Rodar

### Pré-requisitos

* Python 3.10+
* Conta na OpenAI com chave de API válida ou no Open Router

### Passos

```bash
git clone https://github.com/seu-usuario/pokemon-data-agent
cd pokemon-data-agent

cp .env.example .env
# edite o arquivo .env e adicione sua OPENAI_API_KEY

make venv
make install
make ingest       # coleta e armazena dados da PokéAPI
make run          # inicia a API
```

A API ficará disponível em `http://localhost:8000`.

---

## O Agent

O **Pokémon Data Agent** é construído com o **OpenAI Agents SDK (ou Agno)** e possui *tools* para:

* `sql_query(query: str) -> rows`: executa consultas seguras no banco.
* `compare_pokemon(a: str, b: str, metric: str) -> dict`: compara Pokémon por atributos.
* `suggest_team(goal: str) -> list[str]`: sugere um time balanceado.
* `evolution_path(name: str) -> list[str]`: mostra cadeia evolutiva.
* `type_coverage(types: list[str]) -> dict`: calcula cobertura de tipos.

O agent interpreta linguagem natural e responde com evidências reais do banco.

---

## Endpoints da API

| Método | Rota                          | Descrição                                                                            |
| ------ | ----------------------------- | ------------------------------------------------------------------------------------ |
| GET    | `/health`                     | Verifica se a API está no ar                                                         |
| GET    | `/pokemon/{name}`             | Retorna dados do Pokémon                                                             |
| POST   | `/agent/chat`                 | Interage com o agent (`{ "message": "Quem é mais rápido, Jolteon ou Aerodactyl?" }`) |
| GET    | `/team/suggest?goal=balanced` | Sugere um time com cobertura de tipos                                                |

Exemplo de resposta do endpoint `/agent/chat`:

```json
{
  "reply": "Aerodactyl é mais rápido (130) que Jolteon (125).",
  "evidence": [
    {"pokemon": "Aerodactyl", "speed": 130},
    {"pokemon": "Jolteon", "speed": 125}
  ]
}
```

---

## Ingestão de Dados

O script `ingest.py` coleta dados da **PokéAPI** e os armazena em tabelas normalizadas.
Os JSONs brutos são salvos em `./cache/` para evitar chamadas repetidas.

Comando:

```bash
uv run python -m app.ingest --limit 251
```

Tabelas principais:

* `pokemon`
* `pokemon_stat`
* `pokemon_type`
* `species`
* `evolution`

---

## Exemplos de Perguntas ao Agent

* “Quem tem maior defesa, Onix ou Golem?”
* “Mostre a cadeia evolutiva do Eevee.”
* “Monte um time equilibrado da geração 1.”
* “Quais Pokémon têm maior velocidade média?”
* “Qual tipo é mais vulnerável ao gelo?”

---

## Testes

```bash
make test
```

* Testes unitários para funções das *tools*.
* Teste E2E validando ingestão → consulta → resposta do agent.

---

## Avaliação

| Critério     | O que será avaliado                                       |
| ------------ | --------------------------------------------------------- |
| **Ingestão** | Dados corretos e reprocessamento idempotente              |
| **Agent**    | Uso do SDK, clareza das *tools*, evidências nas respostas |
| **Código**   | Estrutura limpa, funções pequenas, tratamento de erro     |
| **API**      | Endpoints funcionais e coerentes                          |
| **Testes**   | Cobertura básica e execução rápida                        |
| **DX**       | Facilidade de setup (`make up`, `.env`, etc.)             |

---

## Bônus (não obrigatório)

---

## Licença

Este projeto é apenas para fins de entrevista técnica.
Você é livre para adaptar, melhorar ou expandir conforme desejar.

---

