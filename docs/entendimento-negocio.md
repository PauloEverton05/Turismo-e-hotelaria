# Entendimento de negócio

## Marcos negociais

???+ abstract "Fase 1: Preparação do ambiente"
    - [x] Criar repositório
    - [x] Instalar ferramentas do projeto

???+ abstract "Fase 2: Entendimento de negócio"
    - [x] Elaborar projeto inicial
    - [x] Produzir relatório mineração dos dados
    - [ ] Fotografar o Quadro PBL (hoje é rascunho em texto)

???+ abstract "Fase 3: Entendimento dos dados"
    - [x] Gerar conjunto de dados
    - [x] Produzir relatório qualidade dos dados
    - [x] Realizar descrição dos dados
    - [x] Realizar análise exploratória
    - [x] Produzir relatório do entendimento dos dados

???+ abstract "Fase 4: Preparação dos dados"
    - [x] Gerar script de limpeza dos dados
    - [x] Gerar conjunto de dados processado
    - [x] Produzir relatório preparação dos dados

???+ abstract "Fase 5: Modelagem"
    - [x] Gerar desenho do experimento da modelagem
    - [x] Gerar script da modelagem
    - [x] Produzir relatório com os resultados

???+ abstract "Fase 6: Avaliação"
    - [x] Produzir relatório da avaliação dos resultados
    - [x] Produzir relatório das próximas etapas

???+ abstract "Fase 7: Implementação"
    - [x] Gerar desenho da implementação
    - [x] Desenvolver script da implementação
    - [x] Produzir relatório de monitoramento e manutenção dos modelos
    - [x] Produzir relatório final contendo todas as fases anteriores

## Avaliar a situação

Este projeto é acadêmico: uma disciplina de Aprendizado Não Supervisionado, com uma
dupla de alunos, sem orçamento real nem clientes de negócio reais. A avaliação da
situação, abaixo, descreve como isso molda o projeto, e não finge uma estrutura
corporativa que esta dupla não tem.

### Recursos

#### Equipe técnica

A estrutura de papéis do template (analista de negócios, analista de requisitos,
arquiteto de dados, engenheiro de dados, analista de BI) não se aplica a uma equipe
de duas pessoas: aqui, as duas acumulam todos esses papéis, e os distribuem por
notebook, não por cargo.

| Nome | Função neste projeto | E-mail |
|:---|:---|:---|
| Paulo Everton Souza Rodrigues | Cientista de dados; entendimento de negócio e dos dados, preparação, avaliação e implantação | peverton.ce@gmail.com |
| Luan Victor Gurgel Quintela | Cientista de dados; adaptação inicial dos notebooks e modelagem (clusterização) | luanvictorgurgelquintela@gmail.com |

#### Especialistas de negócio

Não há especialista de negócio externo. A diretoria do resort e do hotel urbano,
descrita no [Canvas do Problema](pbl/canvas-do-problema.md) como solicitante do
projeto, é simulada pela própria dupla: as decisões de negócio (overbooking, canal,
antecedência) são hipóteses de domínio, não validadas com um gestor real de
hotelaria.

#### Equipe de infraestrutura

Não aplicável a este projeto acadêmico. Não há infraestrutura dedicada: os
notebooks rodam no ambiente local de cada integrante, com dependências travadas em
`uv.lock`, e os artefatos pesados (bases processadas, modelo treinado) não são
versionados, de propósito, para manter o repositório leve.

#### Recursos financeiros

Não há recursos financeiros reais neste projeto. A única referência a orçamento é
uma regra de negócio simulada, do Canvas do Problema (campo 10): a empresa
fictícia só financia uma ação diferenciada por grupo, até cinco, o que fixa o
limite máximo de segmentos da modelagem.

### Requisitos do projeto

#### Necessidades de negócio

Hoje as reservas são tratadas com uma política única de overbooking, canal de
venda e antecedência, para os dois hotéis. Isso impede decisões diferentes para
perfis de reserva diferentes: o Canvas do Problema (campos 1 e 2) registra essa
dor como o ponto de partida do projeto.

#### Requisitos funcionais

- Agrupar as reservas em, no máximo, 5 segmentos, sem usar `is_canceled` como
  atributo de entrada.
- Descrever cada segmento em linguagem de negócio (antecedência, tarifa,
  cancelamento) e propor uma ação diferenciada por segmento.
- Classificar uma reserva nova no segmento correto, sem depender de reexecutar a
  modelagem inteira.

Detalhado em [Critérios de sucesso](criterios-sucesso.md).

#### Requisitos técnicos

| Ferramenta | Descrição |
|:---|:---|
| :simple-github: GitHub | Gerenciar o controle de versões. |
| :simple-uv: uv | Para gerenciamento de libs do projeto. |
| :simple-python: uv python | Controle de versões do Python. |
| :simple-jupyter: Jupyter notebook | Ambiente para escrever os scripts necessários. |
| :fontawesome-solid-list-check: Python | ETL, análise exploratória, clusterização, redução de dimensionalidade e regras de associação. |
| :material-bookshelf: Bibliotecas | pandas, numpy, scikit-learn, matplotlib, seaborn, mlxtend (regras de associação), umap-learn (redução de dimensionalidade). |

#### Requisitos de dados

A entidade de análise é a reserva, não o hóspede nem a estadia
([Canvas do Problema](pbl/canvas-do-problema.md), campo 7). A base é a *Hotel
Booking Demand*, pública, CC BY 4.0, com 119.390 reservas e 32 colunas originais,
descrita em [docs/index.md](index.md). Dois riscos de dados vêm do próprio Canvas
(campo 10): a base mistura dois hotéis diferentes numa mesma tabela, e
`is_canceled` é um rótulo que precisa ficar reservado para avaliação, nunca usado
para formar os grupos.

### Riscos

Riscos identificados para este projeto, não uma lista genérica de ciência de
dados:

- **Vazamento de rótulo:** usar `is_canceled` (ou colunas derivadas dele, como
  `reservation_status`) para formar os grupos invalidaria a avaliação inteira, já
  que o cancelamento é o critério usado depois para julgar se os grupos são úteis.
- **Predominância de dados categóricos:** a maioria das colunas é categórica
  (país, canal, tipo de quarto), o que exige cuidado na codificação e pode diluir
  a separação geométrica dos grupos no espaço One-Hot, como de fato ocorreu
  (ver [Avaliação dos resultados](avaliacao.md)).
- **Duplicatas não resolvidas:** 27% da base são linhas idênticas, e a equipe
  decidiu manter todas sem testar o efeito disso na clusterização; é uma pendência
  registrada, não um risco ignorado.
- **Premissas financeiras hipotéticas:** os cenários de ganho de receita por
  segmento são estimativas, não dado real de receita, e não devem ser usados para
  decisão de investimento sem validação com a operação.

### Análise custo-benefício

Projeto acadêmico, sem custo real de execução além do tempo da equipe. O
"benefício" avaliado é o critério de sucesso do Canvas (campo 4): 100% dos grupos
com uma característica clara e uma ação definida. Como exercício de aplicação a um
cenário hipotético, a avaliação final também estimou um retorno financeiro
simulado para a ação de maior escala (política diferenciada de confirmação por
segmento); os números e as premissas estão em
[Avaliação dos resultados](avaliacao.md), e não são repetidos aqui porque
dependem de dado de receita real que este projeto não tem.

## Mineração de dados

A meta de mineração, declarada no Canvas do Problema (campo 5), é agrupamento
(clusterização): encontrar grupos de reservas com características semelhantes e
entender o que diferencia cada grupo. As técnicas usadas para isso são K-Means
(modelo oficial), redução de dimensionalidade (PCA e UMAP, como apoio à leitura
dos grupos) e regras de associação (Apriori, Eclat e FP-Growth, como verificação
independente dos padrões encontrados). O desenho completo do experimento está em
[Modelagem dos dados](modelagem.md).

Não há Data Warehouse corporativo nem times de BI/governança consumindo o
resultado: os artefatos de saída (base segmentada, perfil dos segmentos, modelo
treinado) ficam em `data/processed/` e `models/`, e o consumidor é a própria
equipe técnica, via notebook, como descrito em
[Implementação](implementacao.md).

Mesmo sem dado real de hóspede, as mesmas cautelas éticas se aplicam ao desenho:
a base não traz identificação pessoal do hóspede, e nenhuma informação além da
reserva em si (tarifa, antecedência, canal, país) entra na modelagem.
