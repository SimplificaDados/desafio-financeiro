# Desafio Simplifica Dados — Domínio Financeiro

## O cenário

Você é a **primeira pessoa de dados da Norte Investimentos**, uma plataforma de
investimentos. Não existe time de dados antes de você — existe você, os CSVs que o
pessoal de engenharia despeja todo dia, e uma pergunta de negócio que ninguém
ainda conseguiu responder direito: **quanto cada cliente tem investido, por
setor?** Parece simples. Não é, e é por isso que você foi contratado.

Você não tem acesso ao banco de produção, e não vai ter. A razão é boa: **ninguém
roda query no banco que registra a operação** — uma consulta pesada sua no meio do
dia pode derrubar a plataforma que os clientes usam para investir. Então o time de
engenharia te entrega o dado do jeito seguro: **extrações diárias em CSV**, uma
pasta por dia. É com isso que você trabalha. O resto é com você.

## O que tem aqui

```
.
- README.md                        você está aqui
- .gitignore                       ignora ambiente e ferramenta; repare no que ele NÃO ignora
- data/
  - raw/                           a zona bruta: o dado como ele chegou
    - LEIA-ME.md                   o contrato de extração, escrito pela engenharia da Norte
    - 2026-08-02/                  carga histórica completa (transacoes, ativos, contas)
    - 2026-08-03/ ... 2026-08-12/  extrações diárias (10 dias, mesmos 3 arquivos)
    - entregas_negocio/
      - metas_aporte.csv           planilha que a área de negócio manda à mão
- docs/
  - decisoes.md                    registro das decisões que têm trade-off
  - arquitetura.md                 o mapa do sistema, que você preenche ao longo do curso
```

## O que NÃO tem aqui, e por quê

Este é o ponto mais importante do repositório: **ele vem quase vazio de propósito.**

Não há pasta de ingestão, não há pasta de transformação, não há `requirements.txt`,
não há `.env`, não há `docker-compose.yml`, não há camadas, não há pipeline. Nada
disso foi esquecido. Cada uma dessas peças **você cria ao longo das 8 semanas**, na
semana em que ela passa a fazer sentido — como parte do desafio daquela semana.

O motivo é direto: criar uma pasta é trivial; **decidir que ela precisa existir** é
o conteúdo do curso. Se o repositório já viesse com tudo pronto, você preencheria
formulário em vez de aprender a projetar um sistema de dados.

Então, se durante o curso você sentir falta de alguma estrutura, uma dependência ou
um arquivo de configuração — **provavelmente é porque decidir criar aquilo é o
exercício.** A única coisa que já vem pronta é o dado, porque o dado não é
construído por você: ele é entregue pela Norte.

## Pré-requisitos

Para a Semana 1, só isto:

- **Python 3.10 ou superior**
- **DuckDB CLI** ([instalação](https://duckdb.org/docs/installation/))
- **Git**

Nada além disso por enquanto.

## Como começar

Este repositório é um **template**, não um repositório para você clonar direto. Você
cria o **seu**, a partir dele, e é no seu que você trabalha as 8 semanas.

**1. Crie seu repositório a partir do template.** No topo desta página, clique em
**Use this template** → **Create a new repository**. Dê o nome que quiser, escolha
público ou privado, e crie.

**2. Clone o seu repositório** (o que você acabou de criar, não este):

```bash
git clone <url-do-SEU-repositorio>
cd <nome-do-seu-repositorio>
```

**3. Confira se sua máquina está pronta:**

```bash
python --version    # precisa ser 3.10 ou superior
duckdb --version
git --version
```

Se os três comandos responderem com uma versão, você está pronto. Abra o desafio da
Semana 1 (ele vem pela plataforma) e comece.

> **Por que template e não fork ou clone?** Porque o repositório passa a ser seu de
> verdade: histórico limpo, começando do zero, e você commitando à vontade sem pedir
> permissão a ninguém. Neste aqui você não tem acesso de escrita — e nem precisa ter.
> Commite no seu, semana a semana. O histórico do seu repositório vira o registro de
> como você chegou onde chegou.

## A zona bruta

Antes de mexer no dado, leia o contrato de extração em
[`data/raw/LEIA-ME.md`](data/raw/LEIA-ME.md). Ele explica como as extrações
chegam e qual é a regra da zona bruta.

## Como funcionam os desafios

- O documento de cada semana **vem pela plataforma**, não está neste repositório.
- **Não há correção e não há entrega.** Ninguém vai revisar seu código.
- **As perguntas são o produto, não o código.** O que você constrói é meio; o que
  você aprende a decidir é o fim. O repositório cresce junto com você, semana a
  semana, e no fim do curso ele é seu.
