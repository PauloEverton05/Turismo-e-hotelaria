# Status do projeto

> Atualizado em 16/10/2026, a partir do estado real do repositório (notebooks executados,
> não apenas escritos).

???+ abstract "Fase 1: Preparação do ambiente"
    - [x] Criar repositório
    - [x] Instalar ferramentas do projeto

???+ abstract "Fase 2: Entendimento de negócio"
    - [ ] Elaborar projeto inicial
    - [ ] Produzir relatório mineração dos dados

???+ abstract "Fase 3: Entendimento dos dados"
    - [x] Gerar conjunto de dados — `00-dicionario-dados.ipynb`
    - [ ] Produzir relatório qualidade dos dados
    - [x] Realizar descrição dos dados — `data/processed/dicionario.csv`
    - [x] Realizar análise exploratória — `01-analise-exploratoria.ipynb`
    - [ ] Produzir relatório do entendimento dos dados

???+ abstract "Fase 4: Preparação dos dados"
    - [x] Gerar script de limpeza dos dados — `02-ajustes-dados.ipynb`
    - [x] Gerar conjunto de dados processado — `03-preparacao.ipynb`
      (`hotel_bookings_preprocessed.csv`)
    - [ ] Produzir relatório preparação dos dados

???+ abstract "Fase 5: Modelagem"
    - [x] Gerar desenho do experimento da modelagem — ver [modelagem.md](modelagem.md)
    - [ ] Gerar script da modelagem (clusterização) — `04-0-modelagem-cluster.ipynb` e
      `04-1-modelagem-cotovelo.ipynb` em andamento com outro integrante da equipe
    - [x] Redução de dimensionalidade (PCA + UMAP) — `04-2-modelagem-reducao.ipynb`
    - [x] Regras de associação (Apriori/Eclat/FP-Growth) —
      `04-3-modelagem-associacao.ipynb`
    - [ ] Produzir relatório com os resultados

???+ abstract "Fase 6: Avaliação"
    - [ ] Produzir relatório da avaliação dos resultados
    - [ ] Produzir relatório das próximas etapas

???+ abstract "Fase 7: Implementação"
    - [ ] Gerar desenho da implementação
    - [ ] Desenvolver script da implementação
    - [ ] Produzir relatório de monitoramento e manutenção dos modelos
    - [ ] Produzir relatório final contendo todas as fases anteriores
