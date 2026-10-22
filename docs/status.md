# Status do projeto

> Atualizado em 21/10/2026, a partir do estado real do repositório (notebooks executados,
> não apenas escritos).

???+ abstract "Fase 1: Preparação do ambiente"
    - [x] Criar repositório
    - [x] Instalar ferramentas do projeto

???+ abstract "Fase 2: Entendimento de negócio"
    - [x] Elaborar projeto inicial: [index.md](index.md) e
      [Canvas do Problema](pbl/canvas-do-problema.md) preenchidos
    - [ ] Produzir relatório mineração dos dados
    - [ ] Fotografar o Quadro PBL (hoje é rascunho em texto, ver
      [quadro-pbl.md](pbl/quadro-pbl.md))

???+ abstract "Fase 3: Entendimento dos dados"
    - [x] Gerar conjunto de dados: `00-dicionario-dados.ipynb`
    - [ ] Produzir relatório qualidade dos dados
    - [x] Realizar descrição dos dados: `data/processed/dicionario.csv`
    - [x] Realizar análise exploratória: `01-analise-exploratoria.ipynb`
    - [ ] Produzir relatório do entendimento dos dados

???+ abstract "Fase 4: Preparação dos dados"
    - [x] Gerar script de limpeza dos dados: `02-ajustes-dados.ipynb`
    - [x] Gerar conjunto de dados processado: `03-preparacao.ipynb`
      (`hotel_bookings_preprocessed.csv`)
    - [ ] Produzir relatório preparação dos dados

???+ abstract "Fase 5: Modelagem"
    - [x] Gerar desenho do experimento da modelagem: ver [modelagem.md](modelagem.md)
    - [x] Gerar script da modelagem (clusterização): `04-0-modelagem-cluster.ipynb`
      escolheu K-Means, k=5, silhueta 0,126; `04-1-modelagem-cotovelo.ipynb` compara
      os algoritmos
    - [x] Redução de dimensionalidade (PCA + UMAP): `04-2-modelagem-reducao.ipynb`
    - [x] Regras de associação (Apriori/Eclat/FP-Growth):
      `04-3-modelagem-associacao.ipynb`
    - [ ] Produzir relatório com os resultados

???+ abstract "Fase 6: Avaliação"
    - [x] Produzir relatório da avaliação dos resultados: `05-avaliacao.ipynb` e
      [avaliacao.md](avaliacao.md); 5 de 6 critérios atendidos, aprovado para
      implantação
    - [x] Produzir relatório das próximas etapas: seção 5.3 do `05-avaliacao.ipynb`

???+ abstract "Fase 7: Implementação"
    - [x] Gerar desenho da implementação: ver [implementacao.md](implementacao.md)
    - [x] Desenvolver script da implementação: `06-implantacao.ipynb`, função
      `segmentar()`
    - [x] Produzir relatório de monitoramento e manutenção dos modelos:
      [implementacao.md](implementacao.md)
    - [ ] Produzir relatório final contendo todas as fases anteriores
