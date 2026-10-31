# Critérios de sucesso

O sucesso do projeto é dado pela completude dos objetivos de negócio definidos a
seguir. Cada objetivo está no formato de história de usuário (**Como** / **Quero** /
**Para** / **Objetivo**), com os respectivos critérios de aceite.

!!! info "Origem destes critérios"
    [Canvas do Problema](pbl/canvas-do-problema.md), campos 4 (critério de sucesso do
    negócio), 6 (critério de sucesso técnico), 9 (perguntas orientadas a dados) e 11
    (ação esperada por grupo). Tema 02, PBL de Turismo e Hotelaria.

## Visão geral

| # | Objetivo de negócio | Solicitante | Entrega principal | Situação |
|:-:|:---|:---|:---|:-:|
| 1 | Segmentar as reservas em grupos operáveis | Diretoria (simulada) | Modelo K-Means + base segmentada | atendido (5 de 6) |
| 2 | Atribuir uma ação por grupo | Diretoria / revenue management | Tabela de perfil com ação por segmento | atendido (5 de 5) |
| 3 | Encontrar padrões que sustentem a segmentação | Equipe técnica | Regras de associação | atendido (3 de 3) |
| 4 | Classificar reservas novas no grupo certo | Equipe de reservas | Função `segmentar()` | atendido (2 de 2) |

---

## 1. Segmentar as reservas em grupos operáveis

???+ success "Objetivo 1"
    **Como** diretoria do hotel<br>
    **Quero** saber em quantos grupos naturais as reservas se dividem<br>
    **Para** substituir uma política única de overbooking, canal e antecedência por
    políticas diferentes por grupo<br>
    **Objetivo** até 5 grupos, o limite que o orçamento financia

    **Critérios de aceite:**

    - [x] **Entre 2 e 5 segmentos.** *Obtido: 5.*
    - [x] **Menor segmento com, no mínimo, 10% da carteira**, senão não há massa para
      uma ação própria. *Obtido: 12,1%.*
    - [x] **Cancelamento diferencia os segmentos** (razão >= 1,3 entre o maior e o
      menor, p < 0,05). *Obtido: razão 6,62, p ~ 0.*
    - [x] **100% da carteira com segmento atribuído.** *Obtido: 100%, o K-Means não
      produz ruído.*
    - [ ] **Silhueta >= 0,25**, critério não crítico, proposto pela própria equipe no
      Canvas §6. *Obtido: 0,124.* Causa registrada em
      [Avaliação dos resultados](avaliacao.md#criterios-nao-atendidos): o espaço de 98
      colunas, com muito One-Hot, dilui a separação geométrica. Não bloqueia a
      implantação porque os grupos funcionam para o negócio mesmo sem separação
      geométrica forte.

    **Entrega:** `models/model_hotel_k_means.joblib` ·
    `data/processed/segmentos.csv` · [Modelagem dos dados](modelagem.md)

## 2. Atribuir uma ação por grupo

???+ success "Objetivo 2"
    **Como** diretoria / revenue management<br>
    **Quero** que cada grupo tenha uma leitura de negócio e uma ação diferente<br>
    **Para** conseguir decidir, não só descrever<br>
    **Objetivo** transformar um agrupamento estatístico em política operacional, como
    pede o Canvas §11

    **Critérios de aceite:**

    - [x] **Perfil de negócio por segmento** (taxa de cancelamento, antecedência,
      tarifa, hóspedes, solicitações especiais). *Obtido: `perfil_segmentos.csv`, 5 de
      5 segmentos descritos.*
    - [x] **Uma ação diferenciada por segmento**, exigida pelo Canvas §4 (meta: 100%
      dos grupos com ação clara). *Obtido: 5 de 5, tabela abaixo.*

    **Entrega:** `data/processed/perfil_segmentos.csv` · tabela "Ação por segmento",
    nesta página

    ### Ação por segmento

    Segmentos renomeados de A (mais risco) a E (menos risco) pela taxa de
    cancelamento, como em [Avaliação dos resultados](avaliacao.md).

    | Segmento | Carteira | Cancelamento | Antecedência mediana | Tarifa mediana | Ação proposta |
    |:-:|:-:|:-:|:-:|:-:|:---|
    | A | 12,1% | 99,7% | 186 dias | US$ 86 | Reserva sem garantia real (Non Refund) com muita antecedência: cobrar confirmação antecipada ou depósito, e liberar o quarto para overbooking controlado desde já |
    | B | 27,1% | 37,4% | 57 dias | US$ 99 | Risco moderado-alto: política de cancelamento intermediária, lembrete de confirmação a partir de 30 dias antes |
    | C | 25,2% | 30,3% | 62 dias | US$ 120 | Risco moderado, tarifa mais alta: manter política padrão, priorizar para upsell em vez de desconto |
    | D | 20,9% | 23,9% | 99 dias | US$ 79 | Antecedência alta mas risco moderado-baixo: pouco overbooking, foco em canal de venda próprio |
    | E | 14,7% | 15,1% | 10 dias | US$ 89 | Última hora e risco baixo: liberar disponibilidade sem reserva extra de segurança, confirmação automática |

## 3. Encontrar padrões que sustentem a segmentação

???+ success "Objetivo 3"
    **Como** equipe técnica<br>
    **Quero** saber que combinações de características aparecem juntas com mais
    frequência do que o acaso explicaria<br>
    **Para** ter uma leitura da base que não dependa de abrir o modelo de
    clusterização<br>
    **Objetivo** validar, por um caminho independente, o que diferencia cada grupo

    **Critérios de aceite:**

    - [x] **Ao menos 10 regras fortes** (suporte >= 15%, confiança >= 60%, lift >=
      1,2). *Obtido: 523 regras.*
    - [x] **Apriori, Eclat e FP-Growth encontram os mesmos conjuntos frequentes.**
      *Obtido: conferido no `04-3-modelagem-associacao.ipynb`.*
    - [x] **Ao menos um padrão ligado a cancelamento.** *Obtido: `country=PRT` com
      tipo de quarto ou canal, lift ~1,8 para cancelamento; `lead_time=baixo`, lift
      ~1,3 para não cancelamento.*

    **Entrega:** `data/processed/regras_associacao.csv` ·
    [Modelagem dos dados](modelagem.md)

## 4. Classificar reservas novas no grupo certo

???+ success "Objetivo 4"
    **Como** equipe de reservas<br>
    **Quero** saber o segmento de uma reserva nova, antes mesmo de ela acontecer<br>
    **Para** aplicar a ação certa desde o momento da reserva<br>
    **Objetivo** que o resultado sirva depois que o notebook de modelagem fechar

    **Critérios de aceite:**

    - [x] **Reserva nova classificada corretamente em pelo menos 85% dos casos**, em
      validação cruzada. *Obtido: 99,7%.*
    - [x] **Função publicada e testada** com reservas de perfil oposto (alto e baixo
      risco). *Obtido: `segmentar()` em `06-implantacao.ipynb`, testada com 2
      reservas de exemplo, cada uma caindo no segmento esperado (A e E).*

    **Entrega:** `notebooks/06-implantacao.ipynb` · [Implementação](implementacao.md)

---

## Consolidado dos critérios de aceite

| Objetivo | Critérios de aceite | Atendidos | Observação |
|:---|:-:|:-:|:---|
| 1. Segmentar as reservas | 5 | 4 | Silhueta não atingiu a meta, critério não crítico |
| 2. Ação por grupo | 2 | 2 | |
| 3. Padrões de coocorrência | 3 | 3 | |
| 4. Classificar reserva nova | 2 | 2 | |
| **Total** | **12** | **11** | |

O confronto detalhado, critério a critério, está em
[Avaliação dos resultados](avaliacao.md).

## Pendências de dados

Critérios que não dependem de análise, e sim de uma decisão da equipe ou de dado que
a base não traz.

| # | Pendência | Destrava |
|:-:|:---|:---|
| 1 | Decidir se as reservas duplicadas (27% da base) ficam ou saem antes de agrupar | Testar a clusterização nas duas versões e comparar a silhueta e o perfil dos segmentos |
| 2 | Validar as premissas financeiras (ganho de receita por segmento) com a operação real | Dado real de receita por ação tomada, hoje é cenário hipotético |
| 3 | Centralizar as constantes de critério (`SILHUETA_MINIMA`, faixa de k) num `src/config.py` | Evita números repetidos entre os notebooks `04-1` e `05` |
