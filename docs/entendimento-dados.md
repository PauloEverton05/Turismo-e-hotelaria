# Entendimento dos dados

Esta página responde a três perguntas sobre o conjunto de dados: **o que existe**, **em que
qualidade** e **o que isso impede ou permite** dentro dos objetivos do projeto.

## Dados iniciais

Os dados analisados aqui vêm de [Fonte dos dados](fonte-dados.md): a base bruta
`data/raw/hotel_bookings.csv`. A apuração desta página está em
`notebooks/01-analise-exploratoria.ipynb`, que parte da base bruta e do dicionário
gerado em `00-dicionario-dados.ipynb`.

## Descrição dos dados

### Grão

Cada linha é **uma reserva** de hotel (resort ou urbano). Não há coluna
identificadora única de reserva na origem.

A tabela é **achatada**: traz embutidas, numa única linha, as dimensões de
calendário, hóspedes, canal de venda, quarto e desfecho da reserva. Não há
normalização a desfazer.

### Grupos de colunas

| Dimensão | Exemplos de colunas |
|:---|:---|
| Desfecho (vazamento, reservado para avaliação) | `is_canceled`, `reservation_status`, `reservation_status_date` |
| Antecedência e calendário | `lead_time`, `arrival_date_year/month/week_number/day_of_month` |
| Estadia e hóspedes | `stays_in_weekend_nights`, `stays_in_week_nights`, `adults`, `children`, `babies` |
| Canal e origem | `country`, `market_segment`, `distribution_channel`, `agent`, `company` |
| Quarto e tarifa | `reserved_room_type`, `assigned_room_type`, `deposit_type`, `adr` |
| Métricas | `adr`, `total_nights`, `total_guests` (as duas últimas, derivadas) |

### Cobertura frente aos critérios de sucesso

| Requisito | Situação |
|:---|:---|
| Entidade de análise é a reserva (Canvas §7) | **atendida**, 119.390 linhas, uma por reserva |
| Base sem vazamento na formação dos grupos (Canvas §10) | **atendida**, `is_canceled`, `reservation_status` e `reservation_status_date` identificados e reservados desde a EDA |
| Distinguir reservas por hotel e canal (pergunta 5 do Canvas) | **parcial**, a coluna existe (`hotel`, `market_segment`), mas o recorte por hotel e canal não foi feito na modelagem |
| Identificar reserva individual (chave única) | **inviável**, a base não traz identificador de reserva, o que impede confirmar se uma linha duplicada é a mesma reserva ou um evento repetido |

## Qualidade dos dados

### Valores sentinela

A origem usa o texto `NULL` para marcar ausência, convertido em valor ausente
(`NaN`) já na leitura, em `00-dicionario-dados.ipynb` e em
`01-analise-exploratoria.ipynb`.

| Sentinela | Significado |
|:---|:---|
| `NULL` em `agent` | Reserva sem agência de viagens associada |
| `NULL` em `company` | Reserva sem empresa associada |
| `NULL` em `country` | País de origem não informado |
| `NULL` em `children` | Nenhuma criança informada na reserva |

Essas quatro colunas são a exceção deliberada: a ausência não é descartada nem
imputada com uma média, porque é informativa (por exemplo, "sem agência" é, em si,
um canal de venda).

### Achados que mudam a análise

1. **26,8% de linhas exatamente duplicadas, sem chave de reserva.** A base não
   fornece identificador único; por isso as duplicatas são contadas, mas mantidas
   em toda a cadeia (EDA, preparação e modelagem), como registrado em
   `01-analise-exploratoria.ipynb`. É uma decisão aberta, não resolvida: a
   clusterização não foi testada na versão sem duplicatas.
2. **Taxa geral de cancelamento de 37%.** Mede o tamanho do problema de negócio,
   não é usada como atributo de entrada em nenhuma etapa de agrupamento, só como
   critério de avaliação em [Avaliação dos resultados](avaliacao.md).
3. **Ausência concentrada em `agent`, `company` e `country`.** São exatamente as
   três colunas de alta cardinalidade da base; a ausência nelas é tratada como
   categoria própria (`Sem agência`, `Sem empresa`, `País não informado`), em vez
   de ser descartada ou imputada estatisticamente.
4. **`is_canceled` e `reservation_status`/`reservation_status_date` são vazamento
   de rótulo.** Descrevem o desfecho da reserva, registrado depois dela ter
   acontecido; por isso são excluídas da matriz de modelagem em
   [Preparação dos dados](preparacao.md) e só voltam na avaliação.

## Dimensões-chave

A dimensão que exigiu tratamento próprio é `country`: tem 178 categorias, a maior
cardinalidade entre as colunas nominais da base. Um One-Hot direto sobre ela
criaria mais colunas do que todas as outras variáveis nominais somadas, e a cauda
tem países com 1 ou 2 reservas, insuficientes para sustentar um perfil de grupo.

| Valor | Descrição | Regra de derivação |
|:---|:---|:---|
| 15 países originais mais frequentes | mantidos com o próprio código do país | estão entre os 15 valores mais frequentes de `country` |
| `Outro` | agrupa os 163 países restantes | não está entre os 15 mais frequentes |

A derivação é feita em `03-preparacao.ipynb`, descrita em
[Preparação dos dados](preparacao.md#reduzindo-a-cardinalidade-de-country).

## Limitações

- **Sem chave de reserva**, não é possível confirmar se uma duplicata exata é a
  mesma reserva lançada duas vezes ou duas reservas de negócio diferentes com
  atributos iguais. Afeta a decisão de manter ou remover duplicatas antes de
  agrupar, registrada como pendência em
  [Critérios de sucesso](criterios-sucesso.md#pendencias-de-dados).
- **Sem distribuição por hotel e canal de venda já cruzada no desenho da EDA**,
  a pergunta 5 do Canvas do Problema fica sem resposta nesta cadeia.
- **Sem dado de receita real por ação tomada**, qualquer leitura financeira do
  projeto (ver [Avaliação dos resultados](avaliacao.md)) é hipotética, não
  validada com a operação.
