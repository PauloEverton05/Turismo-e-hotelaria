# Análise exploratória

Esta página resume **o desenho** da análise exploratória: o que o notebook responde e as
decisões metodológicas que valem para toda a análise. Os resultados em si ficam nas figuras
de `docs/imagens/eda/` e nas tabelas de `reports/tables/eda-exploratoria/`.

## Mapa dos notebooks

| Notebook | Objetivo | Recorte | Saída principal |
|:---|:-:|:---|:---|
| `00-dicionario-dados.ipynb` | 1 | base bruta, 32 colunas | `data/processed/dicionario.csv` |
| `01-analise-exploratoria.ipynb` | 1 | base bruta + 4 colunas derivadas | figuras em `docs/imagens/eda/`, tabelas em `reports/tables/eda-exploratoria/` |

A coluna **Objetivo** referencia a numeração dos [critérios de sucesso](criterios-sucesso.md).

## Desenho da EDA

O notebook `01-analise-exploratoria.ipynb` segue um roteiro de 7 etapas numeradas,
mais uma síntese final, todas aplicadas às 119.390 reservas e às 4 colunas
derivadas na própria carga (`arrival_date`, `arrival_month`, `total_nights`,
`total_guests`).

| Etapa | Técnica | O que responde |
|:---|:---|:---|
| 1. Distribuição dos atributos | contagem de linhas, colunas, tipos e duplicatas exatas | qual é o tamanho e a forma geral da base? |
| 2. Univariada | gráfico de barras para nominais (10 categorias mais frequentes), histograma e boxplot para numéricas | como cada variável se distribui, isolada? |
| 3. Bivariada | mediana de `lead_time`, `total_nights`, `total_guests` e `adr`, comparada entre reserva cancelada e não cancelada | o cancelamento muda o perfil típico da reserva? |
| 4. Multivariada | correlação de Spearman entre numéricas, binárias e `is_canceled` | que variáveis andam junto com o cancelamento? |
| 5. Valores ausentes | contagem e percentual por coluna | a ausência é pontual ou concentrada em poucas colunas? |
| 6. Valores extremos | regra do intervalo interquartil (IQR), por coluna numérica | há valor fora do padrão, e em que proporção? |
| 7. Seleção preliminar de características | classificação de cada coluna em alvo, vazamento, alta cardinalidade ou candidata | quais colunas podem entrar num modelo, e quais não podem? |
| Síntese | indicadores agregados (taxa geral de cancelamento, segmento mais frequente, antecedência mediana) | resumo numérico para orientar a preparação |

A análise também cruza taxa de cancelamento por categoria (hotel, segmento de
mercado, tipo de depósito) e por mês de chegada, para checar sazonalidade.

### Escolhas metodológicas

- **Spearman em vez de Pearson** na correlação da etapa 4. Pearson mede
  associação linear; Spearman mede associação monotônica (uma variável sobe
  quando a outra sobe, não necessariamente na mesma proporção), o que é mais
  adequado a contagens e indicadores binários como `is_canceled`, que não têm
  relação linear esperada com as demais variáveis.
- **Duplicatas não foram removidas.** A base não tem chave de reserva (ver
  [Entendimento dos dados](entendimento-dados.md)); remover duplicatas sem uma
  regra de negócio poderia eliminar reservas legítimas com os mesmos atributos.
  A etapa 1 só contabiliza (26,8%) e reporta, sem alterar a base.
- **Valores extremos são sinalizados, não removidos.** A etapa 6 aplica a regra
  do IQR só para quantificar quantas observações estão fora da faixa esperada;
  tarifas e estadias extremas podem ser reservas legítimas (grupos grandes,
  estadias longas), então a remoção é uma decisão de negócio adiada para a
  preparação, não uma limpeza automática da EDA.
- **`reservation_status` e `reservation_status_date` ficam na análise, mas
  nunca como explicadoras.** A etapa 7 marca as duas como "excluir por
  vazamento", porque descrevem um estado que só existe depois do desfecho da
  reserva.
- **Percentual, não só contagem, ao comparar categorias de tamanhos muito
  diferentes** (por exemplo, taxa de cancelamento por segmento de mercado):
  contagem absoluta mede volume, não comportamento.

## Reprodução

```bash
uv run jupyter nbconvert --to notebook --execute --inplace notebooks/00-dicionario-dados.ipynb
uv run jupyter nbconvert --to notebook --execute --inplace notebooks/01-analise-exploratoria.ipynb
```

Os notebooks são versionados sem saída (hook `nbstripout` do pre-commit): toda
figura e toda tabela precisam ser persistidas em disco para não se perderem.

## Dados por trás de cada gráfico

A função `salvar()`, definida na primeira célula de código de
`01-analise-exploratoria.ipynb`, grava a figura em `docs/imagens/eda/<nome>.png`
e, quando recebe a tabela de origem, grava também
`reports/tables/eda-exploratoria/<nome>.csv`, separado por `;` e codificado em
UTF-8, no mesmo momento em que a figura é criada. Isso garante que o número do
gráfico e o número da tabela nunca divirjam.
