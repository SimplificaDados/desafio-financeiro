# Registro de decisões

Este arquivo guarda as decisões que moldam o projeto. A ideia não é registrar
tudo — é registrar o que teve **trade-off**: escolhas em que algo foi ganho e
algo foi perdido, e que alguém no futuro (talvez você mesmo, daqui a três meses)
vai querer entender sem ter que reconstruir o raciocínio do zero.

Cada decisão segue o formato abaixo. Copie o modelo, incremente o número e
preencha. Se não houver **Preço**, provavelmente não era uma decisão que valia a
pena registrar — era só uma escolha óbvia.

```markdown
## [numero] — [titulo curto da decisao]
**Data:** YYYY-MM-DD
**Contexto:** que problema fez esta decisao ser necessaria
**Alternativas consideradas:** o que mais estava na mesa, e por que foi descartado
**Decisao:** o que foi escolhido
**Preco:** o que se perde com essa escolha — se nao houver, nao era um trade-off
**Gatilho de revisao:** o que precisaria mudar para reabrir esta decisao
```

---

## 1 — DuckDB como motor analítico local
**Data:** 2026-08-02
**Contexto:** Preciso de um lugar para consultar as extrações em CSV com SQL, sem
depender de acesso ao banco de produção e sem subir infraestrutura. É só eu, uma
máquina, e alguns gigabytes de dado que crescem devagar.
**Alternativas consideradas:**
- *Pandas puro* — resolve leitura e transformação, mas eu perco SQL e passo a
  carregar tudo em memória a cada análise. Some da mesa quando o volume passa do
  que cabe confortavelmente na RAM.
- *Postgres local* — dá SQL de verdade e concorrência, mas exige subir um serviço,
  gerenciar conexões e manter um servidor rodando para um trabalho que é de um
  usuário só. Peso demais para o problema de hoje.
- *SQLite* — leve e embutido como o DuckDB, mas é orientado a linha e transação;
  agregações sobre milhões de linhas ficam lentas. Não foi feito para análise.
**Decisão:** Usar **DuckDB** como motor analítico local. É embutido (um binário,
zero servidor), colunar (feito para agregação), lê CSV e Parquet direto sem
importar, e fala SQL padrão.
**Preço:** DuckDB roda em **um único processo** — não escala horizontalmente e não
é multiusuário. Se amanhã várias pessoas precisarem consultar o mesmo dado ao
mesmo tempo, ou se o volume passar do que uma máquina aguenta, o DuckDB deixa de
servir e o custo de migrar é real.
**Gatilho de revisão:** Reabrir esta decisão quando (a) mais de uma pessoa precisar
consultar o dado simultaneamente, (b) o volume ultrapassar a capacidade de uma
máquina, ou (c) o projeto precisar de um data warehouse gerenciado e compartilhado.
