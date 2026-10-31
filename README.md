# Turismo e Hotelaria: segmentação de reservas de hotel

Projeto de Aprendizado Não Supervisionado (Tema 02: Turismo e hotelaria). Uma
organização que administra um resort e um hotel urbano trata as duas operações com a
mesma política de overbooking, canal de venda e antecedência de reserva. Este projeto
investiga se as 119.390 reservas registradas entre 2015 e 2017 se organizam
naturalmente em perfis distintos, sem usar o desfecho da reserva (cancelada ou não)
para formar esses perfis, e o que cada perfil deveria receber de política
diferenciada.

A base é a *Hotel Booking Demand* (Antonio, de Almeida & Nunes, 2019), pública e de
uso livre (CC BY 4.0). Contexto completo, com o Canvas do Problema e as perguntas
orientadas a dados, em [`docs/index.md`](docs/index.md) e
[`docs/pbl/canvas-do-problema.md`](docs/pbl/canvas-do-problema.md).

## Objetivos e resultados chave

- Descobrir quantos perfis de reserva existem na carteira, no máximo cinco por
  restrição orçamentária (uma ação diferenciada custa por grupo).
  - Agrupar as reservas sem usar `is_canceled` como atributo de entrada.
  - Caracterizar cada grupo em linguagem de negócio (antecedência, tarifa,
    cancelamento).
- Verificar se o cancelamento diferencia os grupos encontrados.
  - Medir a razão de cancelamento entre o grupo de maior e o de menor risco.
  - Confirmar a diferença com teste estatístico (qui-quadrado).
- Entregar o resultado de forma que sirva depois que o notebook de modelagem
  fechar.
  - Publicar uma função que classifica reservas novas no segmento certo.
  - Validar a classificação em reservas que o modelo nunca viu.

Resultados obtidos, com números e critério de aceite, em
[`docs/avaliacao.md`](docs/avaliacao.md) e no relatório consolidado
[`docs/relatorio-final.md`](docs/relatorio-final.md).

## Conteúdo

- `00-dicionario-dados.ipynb`: gera o dicionário de dados a partir das 32 colunas
  originais da base.
- `01-analise-exploratoria.ipynb`: análise exploratória da base bruta.
- `02-ajustes-dados.ipynb`: limpeza dos dados (`hotel_bookings_tratado.csv`).
- `03-preparacao.ipynb`: matriz pré-processada de 98 colunas, com `is_canceled`
  reservado fora do arquivo de modelagem.
- `04-0-modelagem-cluster.ipynb` e `04-1-modelagem-cotovelo.ipynb`: comparam
  algoritmos de agrupamento e escolhem o modelo oficial (K-Means, k=5).
- `04-2-modelagem-reducao.ipynb`: redução de dimensionalidade (PCA + UMAP), apoio à
  leitura dos grupos.
- `04-3-modelagem-associacao.ipynb`: regras de associação (Apriori/Eclat/FP-Growth)
  entre características das reservas.
- `05-avaliacao.ipynb`: confronta o modelo escolhido com os critérios de aceite do
  Canvas do Problema.
- `06-implantacao.ipynb`: publica a função `segmentar()`, que classifica reservas
  novas no segmento correto.
- `07_modelagem_bonus.ipynb`: experimento complementar, compara outros algoritmos
  de agrupamento; não substitui o modelo oficial.

Relatório consolidado das fases 1 a 6 em
[`docs/relatorio-final.md`](docs/relatorio-final.md).

## Utilização

Este projeto usa o [uv](https://docs.astral.sh/uv/) para gerenciar dependências e ambiente virtual.

Passos básicos com o `uv`:

```bash
# Instalar o uv (caso ainda não tenha)
# https://docs.astral.sh/uv/getting-started/installation/

# Criar o ambiente virtual e instalar todas as dependências (usa o uv.lock)
uv sync

# Instalar os hooks de pre-commit (ruff + nbstripout)
uv run pre-commit install

# Listar as tarefas disponíveis do projeto
uv run invoke --list

# Exemplos de tarefas
uv run invoke lab     # Abre o JupyterLab
uv run invoke app     # Executa a aplicação Streamlit
uv run invoke docs    # Serve a documentação localmente
uv run invoke lint    # Verifica o código com o ruff
uv run invoke test    # Executa os testes com o pytest

# Adicionar uma nova dependência
uv add <pacote>

# Adicionar uma dependência de desenvolvimento ou de documentação
uv add --group dev <pacote>
uv add --group docs <pacote>
```

A versão do Python utilizada está fixada em [`.python-version`](.python-version); o `uv` instala e usa essa versão automaticamente.

## Desenvolvedores
 - [Paulo Everton Souza Rodrigues](https://github.com/PauloEverton05)
 - [Luan Victor Gurgel Quintela](https://github.com/luanv18)

## Organização de diretórios

```
.
├── .github/                # Templates de issues/PRs e CODEOWNERS
├── data/                   # Diretório contendo todos os arquivos de dados (Geralmente está no git ignore ou git LFS)
│   ├── external/           # Arquivos de dados de fontes externas
│   ├── processed/          # Arquivos de dados processados
│   └── raw/                # Arquivos de dados originais, imutáveis
├── docs/                   # Documentação do projeto publicada com o MkDocs
├── models/                 # Modelos treinados e serializados, predições ou resumos de modelos
├── notebooks/              # Diretório contendo todos os notebooks utilizados nos passos
├── references/             # Dicionários de dados, manuais e todo o material exploratório
├── reports/                # Análises geradas como html, latex, etc
│   └── figures/            # Imagens utilizadas nas análises
├── src/                    # Código fonte utilizado nesse projeto
│   ├── data/               # Classes e funções utilizadas para download e processamento de dados
│   ├── deployment/         # Classes e funções utilizadas para implantação do modelo
│   └── model/              # Classes e funções utilizadas para modelagem
├── tests/                  # Testes automatizados executados com o pytest
├── .pre-commit-config.yaml # Hooks de qualidade executados antes de cada commit (ruff, nbstripout)
├── .python-version         # Versão do Python utilizada pelo uv para criar o ambiente
├── LICENSE                 # Licença do projeto
├── mkdocs.yml              # Configuração do site de documentação
├── pyproject.toml          # Arquivo de dependências para reprodução do projeto
├── uv.lock                 # Arquivo com subdependências do projeto principal (lockfile do uv)
├── README.md               # Informações gerais do projeto
└── tasks.py                # Arquivo com funções para criação de tarefas utilizadas pelo invoke

```
