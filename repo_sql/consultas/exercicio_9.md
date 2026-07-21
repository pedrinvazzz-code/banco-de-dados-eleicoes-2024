# Exercício 9 — Consultas com JOIN, agregação e filtros

## a) Candidato com maior e menor valor de bens em Minas Gerais

**Maior valor:**

```sql
SELECT C.nm_candidato,
    MAX(B.VR_BEM_CANDIDATO) AS MAIOR_VALOR,
    MIN(B.VR_BEM_CANDIDATO) AS MENOR_VALOR
FROM BEM_CANDIDATO AS B
INNER JOIN CANDIDATO AS C
    ON B.SQ_CANDIDATO = C.SQ_CANDIDATO
WHERE C.SG_UF LIKE 'MG'
GROUP BY C.nm_candidato
ORDER BY MAIOR_VALOR DESC
LIMIT 1;
```

**Resultado:**

| nm_candidato | maior_valor | menor_valor |
|---|---|---|
| CRESIO VIEIRA CAMPOS | 10600250000.00 | 1389.75 |

**Menor valor:**

```sql
SELECT C.nm_candidato,
    MAX(B.VR_BEM_CANDIDATO) AS MAIOR_VALOR,
    MIN(B.VR_BEM_CANDIDATO) AS MENOR_VALOR
FROM BEM_CANDIDATO AS B
INNER JOIN CANDIDATO AS C
    ON B.SQ_CANDIDATO = C.SQ_CANDIDATO
WHERE C.SG_UF LIKE 'MG'
GROUP BY C.nm_candidato
ORDER BY MENOR_VALOR ASC
LIMIT 1;
```

**Resultado:**

| nm_candidato | maior_valor | menor_valor |
|---|---|---|
| ADAIR PEREIRA DE ALMEIDA | 0.00 | 0.00 |

## b) Candidato com o maior bem individual declarado no país

```sql
SELECT c.nm_candidato, p.nr_partido, c.sg_uf, c.nm_ue, b.vr_bem_candidato
FROM candidato AS c
INNER JOIN partido AS p
    ON c.nr_partido = p.nr_partido
INNER JOIN bem_candidato AS b
    ON c.sq_candidato = b.sq_candidato
WHERE b.vr_bem_candidato = (
    SELECT MAX(vr_bem_candidato)
    FROM bem_candidato
)
GROUP BY c.nm_candidato, p.nr_partido, c.sg_uf, c.nm_ue, vr_bem_candidato;
```

**Resultado:**

| nm_candidato | nr_partido | sg_uf | nm_ue | vr_bem_candidato |
|---|---|---|---|---|
| CRESIO VIEIRA CAMPOS | 22 | MG | IPATINGA | 10600250000.00 |

## c) Valor médio de bens declarados por um candidato específico

```sql
SELECT C.nm_candidato, AVG(B.VR_BEM_CANDIDATO)
FROM candidato AS C
INNER JOIN BEM_CANDIDATO AS B
    ON C.SQ_CANDIDATO = B.SQ_CANDIDATO
WHERE C.nm_candidato = 'JOSE LUIZ DATENA'
GROUP BY C.nm_candidato;
```

**Resultado:**

| nm_candidato | avg |
|---|---|
| JOSE LUIZ DATENA | 2553452.685333333333 |

## d) Quantidade de candidatos a vereador por estado

```sql
SELECT CA.SG_UF, COUNT(CA.SQ_CANDIDATO)
FROM cargo AS C
INNER JOIN candidato AS CA
    ON C.CD_CARGO = CA.CD_CARGO
WHERE C.ds_cargo = 'VEREADOR'
GROUP BY CA.SG_UF, C.cd_cargo
ORDER BY CA.SG_UF
LIMIT 10;
```

**Resultado (10 primeiros estados em ordem alfabética):**

| sg_uf | count |
|---|---|
| AC | 2180 |
| AL | 5297 |
| AM | 7661 |
| AP | 1483 |
| BA | 32776 |
| CE | 12192 |
| ES | 9268 |
| GO | 18620 |
| MA | 15506 |
| MG | 68661 |

## e) Total de candidatas mulheres a vereadora nas capitais do Sudeste

```sql
SELECT COUNT(*) AS total_fem_sud
FROM CANDIDATO AS C
INNER JOIN GENERO AS G
    ON C.CD_GENERO = G.CD_GENERO
INNER JOIN CARGO AS CA
    ON C.CD_CARGO = CA.CD_CARGO
WHERE ds_cargo = 'VEREADOR'
AND ds_genero = 'FEMININO'
AND C.SG_UF IN ('SP', 'RJ', 'MG', 'ES')
AND C.NM_UE IN ('SAO PAULO',
                'RIO DE JANEIRO',
                'BELO HORIZONTE',
                'VITORIA');
```

**Resultado:** `637`

## f) Total de coligações distintas registradas

```sql
SELECT COUNT(DISTINCT ds_coligacao)
FROM coligacao;
```

**Resultado:** `5831`

## g) Candidatas mulheres a vereadora em Uberlândia, por partido

```sql
SELECT par.sg_partido AS sigla, COUNT(nr_candidato) AS qtdd
FROM candidato can
INNER JOIN cargo car
    ON can.cd_cargo = car.cd_cargo
INNER JOIN partido par
    ON can.nr_partido = par.nr_partido
INNER JOIN genero AS gen
    ON can.cd_genero = gen.cd_genero
WHERE 1=1
AND ds_genero = 'FEMININO'
AND ds_cargo = 'VEREADOR'
AND nm_ue = 'UBERLÂNDIA'
GROUP BY par.sg_partido;
```

**Resultado (amostra):**

| sigla | qtdd |
|---|---|
| AGIR | 10 |
| AVANTE | 9 |
| CIDADANIA | 3 |
| DC | 9 |
| MDB | 10 |
| MOBILIZA | 10 |
| NOVO | 9 |
| PC do B | 1 |
| PDT | 13 |
| PL | 9 |

## h) Partidos em SP com mais de 10 candidatas mulheres

```sql
SELECT p.sg_partido, COUNT(c.sq_candidato) AS quantidade
FROM candidato AS c
JOIN genero AS g
    ON c.cd_genero = g.cd_genero
JOIN partido AS p
    ON c.nr_partido = p.nr_partido
WHERE 1=1
AND ds_genero LIKE 'FEMININO'
AND c.sg_uf = 'SP'
GROUP BY p.sg_partido
HAVING COUNT(c.sq_candidato) > 10
LIMIT 10;
```

**Resultado (amostra):**

| sg_partido | quantidade |
|---|---|
| AGIR | 475 |
| AVANTE | 1002 |
| CIDADANIA | 323 |
| DC | 526 |
| MDB | 2096 |
| MOBILIZA | 433 |
| NOVO | 495 |
| PC do B | 94 |
| PCO | 21 |
| PDT | 988 |

## i) Candidatos em Cuiabá agrupados por partido e cor/raça

```sql
SELECT p.sg_partido AS sigla, r.ds_cor_raca AS raca, COUNT(c.sq_candidato) AS quantidade
FROM candidato AS c
JOIN partido AS p
    ON c.nr_partido = p.nr_partido
JOIN cor_raca AS r
    ON c.cd_cor_raca = r.cd_cor_raca
WHERE c.nm_ue = 'CUIABÁ'
GROUP BY p.sg_partido, r.ds_cor_raca
LIMIT 10;
```

**Resultado (amostra):**

| sigla | raca | quantidade |
|---|---|---|
| CIDADANIA | BRANCA | 4 |
| CIDADANIA | PARDA | 2 |
| CIDADANIA | PRETA | 5 |
| DC | BRANCA | 9 |
| DC | PARDA | 16 |
| DC | PRETA | 7 |
| MDB | BRANCA | 7 |
| MDB | PARDA | 10 |
| MDB | PRETA | 3 |
| NOVO | BRANCA | 7 |

## j) Quantidade de candidatos por partido em Belo Horizonte

```sql
SELECT p.nr_partido, p.nm_partido, COUNT(c.sq_candidato) AS quantidade
FROM candidato AS c
INNER JOIN partido AS p
    ON c.nr_partido = p.nr_partido
WHERE c.nm_ue LIKE 'BELO HORIZONTE'
GROUP BY p.nr_partido, p.nm_partido
LIMIT 10;
```

**Resultado (amostra):**

| nr_partido | nm_partido | quantidade |
|---|---|---|
| 10 | REPUBLICANOS | 43 |
| 11 | PROGRESSISTAS | 42 |
| 12 | PARTIDO DEMOCRÁTICO TRABALHISTA | 44 |
| 13 | PARTIDO DOS TRABALHADORES | 31 |
| 15 | MOVIMENTO DEMOCRÁTICO BRASILEIRO | 43 |
| 16 | PARTIDO SOCIALISTA DOS TRABALHADORES UNIFICADO | 10 |
| 18 | REDE SUSTENTABILIDADE | 23 |
| 20 | PODEMOS | 45 |
| 21 | PARTIDO COMUNISTA BRASILEIRO | 2 |
| 22 | PARTIDO LIBERAL | 45 |

## k.1) Nome dos candidatos e total de patrimônio declarado

```sql
SELECT c.nm_candidato, SUM(b.vr_bem_candidato) AS patrimonio_total
FROM candidato c
INNER JOIN bem_candidato b
    ON c.sq_candidato = b.sq_candidato
GROUP BY c.nm_candidato
ORDER BY patrimonio_total DESC
LIMIT 10;
```

**Resultado (10 maiores patrimônios):**

| nm_candidato | patrimonio_total |
|---|---|
| CRESIO VIEIRA CAMPOS | 12101378759.75 |
| JOÃO HENRIQUE PINHEIRO | 2851300000.00 |
| CLERI CAMILOTTI | 1542273922.21 |
| EDIOMAR LINO DE AGUIAR | 1500180000.00 |
| DIANILEY DA SILVA PINTO | 1401171000.00 |
| MANOEL ALVES DOS SANTOS | 1200503900.00 |
| JOÃO DIVINO ADORNO | 1102131121.00 |
| GEDEAN FERREIRA AMARO | 1000505000.00 |
| MARIA DE FÁTIMA ROCHA FERREIRA | 830780620.00 |
| MARLON ABDON COQUEIRO PEREIRA | 700038000.00 |

## k.2) Idade média dos candidatos

```sql
SELECT
    AVG(EXTRACT(YEAR FROM CURRENT_DATE) - EXTRACT(YEAR FROM dt_nascimento)) AS idade_media
FROM candidato;
```

**Resultado:** `49.21 anos` (aproximadamente)
