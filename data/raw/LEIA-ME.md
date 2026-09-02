# Contrato de extração — Norte Investimentos

Este documento é do time de engenharia da Norte. Ele descreve **como as extrações
chegam até você e o que cada arquivo contém**. Leia antes de tocar em qualquer
arquivo desta pasta.

## Como as extrações chegam

- **`2026-08-02/`** é a **carga histórica completa**: todos os registros que
  existiam no sistema até 01/08/2026. É a sua fotografia inicial do mundo.
- **As pastas seguintes** (`2026-08-03/` em diante) trazem **apenas os registros
  criados naquele dia**. Não são o estado completo — são o delta do dia.
- Cada pasta tem sempre os **três arquivos** (`transacoes.csv`, `ativos.csv`,
  `contas.csv`), mesmo quando não houve registro novo. Nesse caso, o arquivo vem
  **só com o cabeçalho**. Um arquivo com uma linha só (o cabeçalho) é uma
  informação — "não houve nada novo neste domínio hoje" —, não um erro.
- **Uma extração nunca reescreve a anterior.** Cada dia é um arquivo novo, e os
  dias anteriores permanecem como estão.
- Os identificadores são contínuos: `id_transacao` segue a sequência da carga
  histórica, extração após extração.

## Convenções dos arquivos do sistema

Valem para os três arquivos que saem do sistema (`transacoes.csv`, `ativos.csv`,
`contas.csv`), em qualquer pasta de data:

| Item | Valor |
|---|---|
| Encoding | UTF-8 sem BOM |
| Separador | vírgula |
| Cabeçalho | primeira linha, nomes em `snake_case` sem acento |
| Decimal | ponto (`.`) |
| Data | `YYYY-MM-DD` |
| Timestamp | `YYYY-MM-DD HH:MM:SS` |
| Aspas | só quando o campo contém vírgula |
| Fim de linha | `\n` |

> `entregas_negocio/metas_aporte.csv` **não segue nada disso** — ver a seção final.

## `transacoes.csv` — o livro de operações

O grão é a **operação individual**: uma linha por compra ou venda executada.

| Coluna | Tipo | Descrição |
|---|---|---|
| `id_transacao` | inteiro | identificador único e sequencial da operação; contínuo entre extrações |
| `id_conta` | inteiro | conta que executou a operação — referência a `contas.csv` (`id_conta`) |
| `ticker` | texto | ativo negociado — referência a `ativos.csv` (`ticker`) |
| `tipo_operacao` | texto | `COMPRA` ou `VENDA` |
| `quantidade` | inteiro | número de unidades (cotas/ações) negociadas |
| `preco_unitario` | decimal (4 casas) | preço por unidade no momento da operação, **na moeda do ativo** (ver `ativos.moeda`) |
| `taxa_corretagem` | decimal (4 casas) | taxa cobrada na operação; pode vir vazia |
| `data_operacao` | timestamp | momento em que a operação foi executada |
| `created_at` | timestamp | quando o registro entrou no sistema |
| `updated_at` | timestamp | quando o registro foi atualizado pela última vez |

Observações do contrato:

- `preco_unitario` está sempre na **moeda de negociação do ativo**. Essa moeda
  **não** aparece aqui — ela mora só no cadastro do ativo, em `ativos.moeda`. Para
  saber em que moeda está um preço, junte a transação ao ativo pelo `ticker`.
- `data_operacao` está sempre no passado em relação à data da pasta. Nos dias de
  semana concentra o volume; fins de semana têm movimento residual.

## `ativos.csv` — o cadastro de ativos

O grão é o **ativo**: uma linha por papel disponível para negociação.

| Coluna | Tipo | Descrição |
|---|---|---|
| `ticker` | texto | código do ativo (4 letras + 1 dígito); chave do cadastro |
| `nome_ativo` | texto | nome da empresa ou fundo |
| `setor` | texto | `FINANCEIRO`, `ENERGIA`, `VAREJO`, `SAUDE`, `TECNOLOGIA`, `INDUSTRIA`, `IMOBILIARIO` ou `CONSUMO` |
| `tipo_ativo` | texto | `ACAO`, `FII` ou `ETF` |
| `moeda` | texto | moeda de negociação do ativo: `BRL` ou `USD` |
| `created_at` | timestamp | quando o ativo entrou no cadastro |
| `updated_at` | timestamp | quando o registro foi atualizado pela última vez |

Observação: `moeda` é a única fonte de verdade sobre em que moeda o ativo é
negociado — e, por consequência, sobre a moeda dos preços em `transacoes.csv`.

## `contas.csv` — o cadastro de contas

O grão é a **conta de investimento**: uma linha por conta. Uma conta pertence a um
cliente, e um mesmo cliente pode ter mais de uma conta.

| Coluna | Tipo | Descrição |
|---|---|---|
| `id_conta` | inteiro | identificador único da conta; chave do cadastro |
| `id_cliente` | inteiro | identificador do cliente dono da conta; se repete quando o cliente tem mais de uma conta |
| `perfil_investidor` | texto | `CONSERVADOR`, `MODERADO` ou `ARROJADO` |
| `data_abertura` | data | data de abertura da conta |
| `created_at` | timestamp | quando o registro entrou no sistema |
| `updated_at` | timestamp | quando o registro foi atualizado pela última vez |

Não há nome, documento ou qualquer dado pessoal do cliente nesta base — a conta e o
cliente são identificados apenas por número.

## Como os arquivos se ligam

- `transacoes.id_conta` -> `contas.id_conta` (toda operação pertence a uma conta)
- `transacoes.ticker` -> `ativos.ticker` (toda operação é sobre um ativo)
- `contas.id_cliente` agrupa contas do mesmo cliente
- para saber a moeda de uma transação, o caminho é
  `transacoes.ticker` -> `ativos.ticker` -> `ativos.moeda`

## `entregas_negocio/metas_aporte.csv` — planilha da área de negócio

O que está em `entregas_negocio/` **não vem do sistema**. É planilha que a área de
negócio exporta e envia à mão. Não há contrato, não há garantia de formato, não há
padrão — trate como o que é: um arquivo humano.

A intenção das colunas é registrar, por conta e por mês, a **meta de aporte**
combinada com o cliente e o andamento dela:

| Coluna (como vem no arquivo) | Intenção |
|---|---|
| `Id Conta` | conta a que a meta se refere — deveria casar com `contas.id_conta` |
| `Mês Referência` | mês/ano da meta |
| `Meta Aporte` | valor de aporte esperado no mês |
| `Status` | andamento da meta |
| `Responsável` | pessoa da área de negócio que cadastrou a linha |

Avisos, porque é feito à mão:

- vem em **`;`** como separador e em **encoding legado** (latin-1), não UTF-8;
- os valores de `Meta Aporte` usam padrão brasileiro (`1.500,00`);
- `Mês Referência` mistura formatos diferentes na mesma coluna;
- há linhas em branco, espaços sobrando, `Status` escrito de várias formas e
  `Id Conta` que pode não existir no cadastro.

Em resumo: abrir esse arquivo direto, como se fosse um CSV bem-comportado, não vai
funcionar. Ele exige leitura cuidadosa.

## Regra da zona bruta

**Nada aqui dentro é editado, corrigido ou apagado.** Se o dado veio errado, ele
fica errado aqui. A zona bruta é o registro fiel do que foi entregue — a correção
acontece adiante, nunca na origem.
