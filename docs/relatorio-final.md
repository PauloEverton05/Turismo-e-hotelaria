# Segmentação de reservas hoteleiras, relatório final

Turismo e Hotelaria, Tema 02 do PBL. CRISP-DM, fases 1 a 6. Modelo K-Means, k=5.

> Documento consolidado a partir dos notebooks `00` a `06` e das páginas de
> documentação do projeto. Nenhum número aqui é novo, cada um aponta de volta para
> o notebook ou o relatório que o gerou.

## 1. Sumário executivo

- A carteira de **119.390 reservas** (City Hotel + Resort Hotel) se organiza em
  **5 segmentos** com taxa de cancelamento muito diferente entre eles, de 15,1% a
  99,7%. O modelo é K-Means com k=5, silhueta 0,124.
- A diferença de cancelamento entre o maior e o menor segmento é de **6,62 vezes**,
  com significância estatística (p ~ 0).
- Uma reserva nova é atribuída ao segmento certo em **99,7%** dos casos, validado
  por reserva simulada antes de qualquer ação depender do modelo.
- **5 dos 6 critérios técnicos** do Canvas do Problema foram atendidos. O único
  critério não atendido (silhueta >= 0,25) é não crítico e não impede o uso real da
  função `segmentar()`.
- O cenário financeiro base estima um resultado de **US$ 154.963** no primeiro ano,
  premissa hipotética, a validar com a operação.
- Decisão: **implantar**, registrada em [Avaliação dos resultados](avaliacao.md).

## 2. O problema

Uma organização que administra um resort e um hotel urbano decide políticas de
overbooking, canal de venda e antecedência de forma única para toda a carteira. A
pergunta que origina o projeto: existem perfis de reserva diferentes que pedem
ações diferentes? O critério de sucesso, declarado antes de qualquer modelagem no
[Canvas do Problema](pbl/canvas-do-problema.md): até 5 grupos (o orçamento
financia uma ação por grupo), silhueta razoável, grupos interpretáveis, e
`is_canceled` nunca usado para formar os grupos, só para avaliar depois.

## 3. Metodologia

| Fase | O que decidiu | Evidência |
|:---|:---|:---|
| 1. Entendimento de negócio | Canvas do Problema com objetivo, critério técnico e de negócio, e 5 perguntas orientadas a dados | `docs/pbl/canvas-do-problema.md` |
| 2. Entendimento dos dados | 119.390 reservas, 32 colunas originais, descrição completa de tipo e domínio de cada coluna | `00-dicionario-dados.ipynb`, `01-analise-exploratoria.ipynb` |
| 3. Preparação dos dados | Limpeza (`02`) e matriz de 98 colunas pré-processadas, `is_canceled` reservado fora do arquivo de modelagem | `02-ajustes-dados.ipynb`, `03-preparacao.ipynb` |
| 4. Modelagem | K-Means k=5 escolhido por comparação de algoritmos (`04-0`/`04-1`); redução de dimensionalidade (`04-2`) e regras de associação (`04-3`) como apoio à leitura dos grupos | `04-0`, `04-1`, `04-2`, `04-3` |
| 5. Avaliação | 5 de 6 critérios atendidos, decisão de implantar | `05-avaliacao.ipynb`, `reports/05-avaliacao.json` |
| 6. Implantação | Função `segmentar()` publicada, plano de monitoramento com 4 indicadores | `06-implantacao.ipynb`, `docs/implementacao.md` |

Duas decisões de método sustentam o resto do projeto:

1. **o rótulo foi reservado desde a fase 3.** `is_canceled`, `reservation_status` e
   `reservation_status_date` nunca entram como atributo de nenhum algoritmo de
   agrupamento, só voltam na fase 5 para avaliar os grupos formados;
2. **os critérios de aceitação foram escritos no Canvas**, antes de qualquer
   algoritmo rodar, e conferidos depois contra o resultado real, não ajustados para
   caber nele.

## 4. Resultados

| Resultado | Valor | Critério declarado |
|:---|:---|:---|
| Segmentos | 5 | entre 2 e 5 |
| Menor segmento | 12,1% da carteira | >= 10% |
| Razão de cancelamento (maior/menor segmento) | 6,62 (p ~ 0) | >= 1,30, p < 0,05 |
| Reserva nova classificada corretamente | 99,7% | >= 85% |
| Silhueta | 0,124 | >= 0,25 (não atendido, não crítico) |
| Cobertura da carteira | 100% | 100% |

### Os 5 segmentos

Renomeados de A (mais risco) a E (menos risco) pela taxa de cancelamento. Tabela
completa de ação por segmento em
[Critérios de sucesso](criterios-sucesso.md#acao-por-segmento).

| Segmento | Carteira | Cancelamento | Antecedência mediana | Tarifa mediana |
|:-:|:-:|:-:|:-:|:-:|
| A | 12,1% | 99,7% | 186 dias | US$ 86 |
| B | 27,1% | 37,4% | 57 dias | US$ 99 |
| C | 25,2% | 30,3% | 62 dias | US$ 120 |
| D | 20,9% | 23,9% | 99 dias | US$ 79 |
| E | 14,7% | 15,1% | 10 dias | US$ 89 |

## 5. Insights

| # | Insight | Evidência | Dono |
|:-:|:---|:---|:---|
| 1 | Antecedência alta é o traço mais ligado a cancelamento | Segmento A, 186 dias de antecedência mediana, 99,7% de cancelamento | Revenue management |
| 2 | Reserva de última hora quase não cancela | Segmento E, 10 dias de antecedência, 15,1% de cancelamento; regra de associação com lift ~1,3 para não cancelamento | Operação de reservas |
| 3 | `country=PRT` combinado com tipo de quarto ou canal eleva o risco | Regra de associação, lift ~1,8 para cancelamento | Revenue management |
| 4 | As variáveis mais relevantes para separar perfis coincidem em PCA e UMAP | 13 de 15 variáveis no topo de ambos: `hotel`, `deposit_type`, `market_segment`, `country`, datas de chegada | Área de dados |
| 5 | O padrão mais forte de cancelamento (depósito não reembolsável) não aparece em nenhuma regra | `deposit_type=Non Refund`, 12,2% das reservas, 99,4% de cancelamento, abaixo do corte de suporte mínimo de 15% | Área de dados |

## 6. As perguntas do Canvas, respondidas

| Pergunta (Canvas §9) | Resposta medida nesta cadeia |
|:---|:---|
| Quais perfis de reserva podem ser identificados? | 5 perfis, descritos na tabela de segmentos acima |
| Quantos grupos, respeitando o limite de 5? | 5 |
| Quais características diferenciam cada grupo? | Antecedência, tarifa e taxa de cancelamento, com leitura de 13 variáveis em comum entre PCA e UMAP |
| Quais grupos cancelam diferente? | Sim, de 15,1% a 99,7%, razão de 6,62 vezes, p ~ 0 |
| Como os grupos se distribuem entre hotel e canal? | Não recortado nesta cadeia; fica como pendência de dados em `criterios-sucesso.md` |

## 7. Implantação

- **Função `segmentar()`**, em `06-implantacao.ipynb`: recebe reservas novas, sem
  exigir todos os campos preenchidos, e devolve o segmento e o perfil dele;
- **pré-processador reconstruído e conferido**: o notebook afirma, com `assert`,
  que reproduz exatamente a base salva na fase 3, antes de classificar qualquer
  reserva nova;
- **consumidor é a própria equipe técnica e de reservas**, rodando o notebook sob
  demanda, reserva a reserva ou em lote via CSV; não há serviço web nesta fase;
- **retreinamento semestral**, ou antes se a acurácia cair abaixo de 85% ou um
  segmento passar de 90% da carteira.

## 8. Monitoramento e manutenção

| Indicador | Periodicidade | Gatilho | Ação |
|:---|:---|:---|:---|
| Concordância do modelo carregado com `segmentos.csv` | a cada carregamento | abaixo de 100% | reexecutar `04-0` e regravar os artefatos |
| Acurácia de reserva nova classificada | semestral | abaixo de 85% | retreinar o classificador |
| Tamanho do maior segmento | a cada retreino | acima de 90% | revisar k ou a regra de aprovação |
| Taxa de cancelamento por segmento | mensal | desvio do perfil histórico | ajustar política de overbooking do segmento |

## 9. O que não foi entregue

- **silhueta abaixo da meta** (0,124 contra 0,25), não crítico, com causa
  registrada: o espaço de 98 colunas, com muito One-Hot, dilui a separação
  geométrica entre grupos;
- **distribuição por hotel e canal de venda** (pergunta 5 do Canvas) não foi
  recortada nesta cadeia;
- **a decisão sobre duplicatas** (27% da base) não foi tomada: os grupos foram
  formados com a base inteira, sem testar a versão sem duplicatas;
- **premissas financeiras** (ganho de receita por ação) são hipotéticas, a validar
  com a operação real antes de qualquer decisão de investimento;
- **Quadro PBL** ainda é um rascunho em texto
  ([quadro-pbl.md](pbl/quadro-pbl.md)), não a foto do quadro físico da
  equipe;
- **autoavaliação do Luan Victor Gurgel Quintela**, em
  [Avaliação dos resultados](avaliacao.md#registro-do-percurso-pbl-contribuicao-individual-e-autoavaliacao),
  ainda não foi escrita por ele em primeira pessoa.

## 10. Como reproduzir

```bash
uv sync
uv run jupyter nbconvert --to notebook --execute --inplace notebooks/02-ajustes-dados.ipynb
uv run jupyter nbconvert --to notebook --execute --inplace notebooks/03-preparacao.ipynb
uv run jupyter nbconvert --to notebook --execute --inplace notebooks/04-0-modelagem-cluster.ipynb
uv run jupyter nbconvert --to notebook --execute --inplace notebooks/05-avaliacao.ipynb
uv run jupyter nbconvert --to notebook --execute --inplace notebooks/06-implantacao.ipynb
```

Semente fixa em 42 nos algoritmos que aceitam `random_state`. A reexecução completa
(`00` a `06`) regrava todos os artefatos de `data/processed/` e `models/` a partir
da base bruta, sem exigir nenhum passo manual.
