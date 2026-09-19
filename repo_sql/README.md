# Banco de Dados — Eleições Municipais 2024

Exercício da disciplina de **Banco de Dados**, do curso de **Gestão da Informação da Universidade Federal de Uberlândia (UFU)**.

> 💡 **As consultas foram escritas manualmente por mim**, como parte do aprendizado de SQL e modelagem relacional na disciplina.

## Sobre

O exercício usa uma base de dados relacional com informações de candidatos às eleições municipais de 2024 no Brasil (dados públicos do TSE), envolvendo tabelas de candidatos, partidos, cargos, coligações e bens declarados. O objetivo foi praticar `JOIN`, agregações (`GROUP BY`, `HAVING`), subconsultas e funções de data.

## Estrutura

```
banco-de-dados-eleicoes-2024/
├── database/
│   └── schema_ddl.sql       → definição das tabelas (DDL)
└── consultas/
    └── exercicios_.md       → consultas SQL com resultados e explicações
```

## Modelo de dados

O schema `ELEICAO_2024` é composto pelas tabelas:

- `CANDIDATO` — dados dos candidatos (nome, UF, data de nascimento, cargo, partido, etc.)
- `BEM_CANDIDATO` — bens patrimoniais declarados por cada candidato
- `PARTIDO`, `FEDERACAO`, `COLIGACAO` — informações partidárias
- `CARGO` — cargos disputados (prefeito, vice-prefeito, vereador)
- `GENERO`, `COR_RACA`, `ESTADO_CIVIL`, `GRAU_INSTRUCAO`, `OCUPACAO` — atributos demográficos dos candidatos

O arquivo [`database/schema_ddl.sql`](database/schema_ddl.sql) contém apenas a definição das tabelas (`CREATE TABLE`), sem os dados.

> ⚠️ **Sobre os dados:** o dump completo com os registros (mais de 900 mil linhas de `INSERT`) tem cerca de 170 MB, acima do limite do GitHub para arquivos individuais, e não está incluído neste repositório. Os dados foram fornecidos pelo professor da disciplina, com base em informações públicas do TSE. Para reproduzir as consultas, é necessário popular o schema com a base de dados completa.

## Como reproduzir

1. Crie o schema executando o DDL:
   ```bash
   psql --host=localhost --username=postgres -q --file=database/schema_ddl.sql
   ```
2. Popule as tabelas com os dados da base de eleições 2024 (arquivo fornecido separadamente pela disciplina).
3. Execute as consultas listadas em [`consultas/exercicio_9.md`](consultas/exercicio_9.md).

## Consultas

O exercício cobre 11 consultas (itens a–k), incluindo:

- Maior e menor patrimônio declarado por estado
- Candidato com o maior bem individual do país
- Contagem de candidatos por cargo, estado, gênero e cor/raça
- Filtros combinando múltiplas condições (`WHERE`, `HAVING`)
- Subconsulta para identificar valores máximos
- Cálculo de idade média usando funções de data (`EXTRACT`)

Veja o detalhamento completo com as queries e os resultados em [`consultas/exercicios_.md`](consultas/exercicios_.md).

## Tecnologias

- **PostgreSQL**
- SQL padrão (DDL, DML, JOINs, agregações, subconsultas)

---

📫 Encontre meus outros projetos de dados em [github.com/pedrinvazzz-code](https://github.com/pedrinvazzz-code)
