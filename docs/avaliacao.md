# Avaliação dos resultados

Enquanto a avaliação feita em [Modelagem dos dados](modelagem.md) é **técnica**, esta página
olha para o **projeto como um todo**: os resultados atendem aos objetivos de negócio, o
processo foi conduzido corretamente, e o que fazer em seguida.

!!! tip "Como preencher esta página"
    Esta é a fase em que o projeto é confrontado com o que prometeu. Preencha com os
    [critérios de sucesso](criterios-sucesso.md) abertos ao lado.

    Regras práticas:

    - avalie **critério a critério**, não em bloco: "o projeto foi bem" não é avaliação;
    - critério não atendido é registrado como **não atendido**, com a causa: reescrever o
      critério para caber no resultado é a forma mais comum de um projeto falhar em
      silêncio;
    - separe o que falhou por **limitação de dado** do que falhou por **decisão de
      condução**: as ações são diferentes;
    - a revisão de processo é sobre o **como**, e vale mesmo quando tudo deu certo;
    - termine com uma decisão explícita: implantar, iterar ou encerrar.

## Confronto com os critérios de aceite

Avaliação executada em `notebooks/05-avaliacao.ipynb`, registrada em
`reports/05-avaliacao.json`, contra os itens 4 e 6 do
[Canvas do Problema](pbl/canvas-do-problema.md). Modelo avaliado: K-Means, k=5
(vencedor escolhido em `04-0-modelagem-cluster.ipynb`).

| Objetivo (Canvas) | Critério de aceite | Situação | Evidência |
|:---|:---|:---|:---|
| 6 | Entre 2 e 5 segmentos | atendido (5 segmentos) | `05-avaliacao.ipynb`, tabela de critérios |
| 6 | Menor segmento com, no mínimo, 10% da carteira | atendido (12,1%) | mesma tabela |
| 9.4 | Cancelamento diferencia os segmentos (razão >= 1,3 e p < 0,05) | atendido (razão 6,62x, p ~ 0) | mesma tabela |
| 4 | Reserva nova classificável corretamente (>= 85%) | atendido (99,7% em validação cruzada) | mesma tabela |
| 6 | Silhueta >= 0,25 (critério não crítico) | não atendido (0,124) | mesma tabela; já esperado desde o `04-1` |
| 4 | 100% da carteira com segmento atribuído | atendido (K-Means não produz ruído) | mesma tabela |

**Leitura consolidada:** 5 dos 6 critérios foram atendidos, e o único que falhou é o
não crítico (silhueta). Isso significa que, mesmo com separação geométrica fraca no
espaço de 98 colunas (esperado dado o peso do One-Hot), os segmentos **funcionam
para o negócio**: cancelam de forma diferente entre si (6,6x de diferença, com
significância estatística) e uma reserva nova é atribuída ao segmento certo quase
sempre. O cenário financeiro base também se paga no primeiro ano
(resultado estimado de ~US$ 155 mil, premissas hipotéticas, a validar).

### Critérios não atendidos

| Critério | Causa | Natureza |
|:---|:---|:---|
| Silhueta >= 0,25 | O espaço de 98 colunas, com muito One-Hot, dilui a separação geométrica entre grupos | restrição técnica, documentada no `04-2` e no `04-1` |

Destravaria com redução de dimensionalidade (as bases `hotel_bookings_pca.csv` e
`hotel_bookings_umap.csv`, já geradas no `04-2`) antes de agrupar; depende de rodar
o `04-0` de novo sobre essas bases reduzidas, o que não bloqueia a implantação
porque o critério é não crítico.

## Revisão do processo

Retrospectiva da condução, independente do resultado. Lista completa de lacunas
numeradas no `05-avaliacao.ipynb`.

- **O que funcionou:** dividir o trabalho por notebook numerado (00 a 06), cada um
  com uma única responsabilidade, permitiu que dois integrantes avançassem em
  paralelo (um na clusterização, outro na redução de dimensionalidade e nas regras
  de associação) sem travar um no outro. Reservar `is_canceled` desde o `02`
  evitou qualquer vazamento na formação dos grupos.
- **O que atrasou:** os artefatos gerados (`data/processed/*.csv`, `models/*.joblib`)
  não são versionados no git, de propósito; isso exigiu reexecutar a cadeia inteira
  (00 a 04-0) do zero sempre que o checkout mudava de máquina ou de branch, com
  execuções de 7 a 8 minutos só para o `04-0`.
- **O que foi esquecido:** o Quadro PBL (seção 5 do PBL) ainda é um rascunho em
  texto, não a foto do quadro físico da equipe; e nenhuma das constantes de
  critério (`SILHUETA_MINIMA`, faixa de k, teto de ruído) está centralizada em um
  `src/config.py`, como recomenda a lacuna 6 do `05-avaliacao`.

??? note "Perguntas que ajudam nesta revisão"
    - alguma conclusão está apoiada em dado que não foi conferido?
    - as decisões metodológicas relevantes ficaram registradas, ou só existem na cabeça de
      quem executou?
    - alguém que não participou consegue reproduzir os resultados a partir do repositório?
    - o tratamento de dados pessoais e sigilosos foi adequado em todas as etapas?

## Registro do percurso PBL: contribuição individual e autoavaliação

O PBL exige, como parte do produto final, o registro de quem fez o quê e a
autoavaliação de cada integrante, não apenas o resultado técnico. Esta seção
cobre essa exigência a partir de evidência objetiva: o histórico real de
commits do repositório (`git log --numstat`) e o estado não commitado deste
ambiente de trabalho, author-atribuído pela configuração local do git
(`git config user.name/user.email` → `PauloEverton05 <peverton.ce@gmail.com>`).

**Equipe:** Paulo Everton Souza Rodrigues (`PauloEverton05` / `Paulo Everton`) ·
Luan Victor Gurgel Quintela (`Luan Victor`)

### Divisão de trabalho por etapa (evidência: commits)

| Commit | Data | Autor | Etapa / notebook | O que foi feito |
|:---|:---|:---|:---|:---|
| `21c954f` | 25/09 | Paulo Everton | Scaffold inicial | Estrutura do repositório (fork do template), docs base, CI, licença |
| `daefb3d` | 16/10 | Paulo Everton | `00-dicionario-dados`, `01-analise-exploratoria` | Criação dos notebooks de dicionário de dados e EDA, com as 38 figuras de `docs/imagens/eda/` |
| `19a48b4`→`2b80dc0`→`49e07b4` | 16/10 | Luan Victor | `02-ajustes-dados` | Criação e reescrita do notebook de limpeza/ajuste de dados (3 iterações no mesmo dia) |
| `fdad745` | 16/10 | Paulo Everton | `02-ajustes-dados`, `03-preparacao` | Ajustes na limpeza e criação do notebook de preparação (encoding/padronização) |
| `1192a21` | 17/10 | Luan Victor | `00-dicionario-dados`, `01-analise-exploratoria` | Adaptação dos notebooks genéricos do template para o domínio de hotelaria |
| `815bc2f` | 17/10 | Luan Victor | `04-0-modelagem-cluster`, `04-1-modelagem-cotovelo` | Decisão do número de grupos (método do cotovelo) e treino do K-Means (k=5), o núcleo da clusterização |
| `94d9f03` | 17/10 | Paulo Everton | `04-2-modelagem-reducao`, `04-3-modelagem-associacao` | PCA/UMAP para interpretação dos grupos e regras de associação (Apriori/FP-Growth) |
| `b4398e3` | 21/10 | Paulo Everton | Canvas do Problema, Quadro PBL (rascunho) | Preenchimento dos 11 campos do Canvas e redação do rascunho do Quadro PBL |
| `7b9d9a2` | 21/10 | Paulo Everton | `05-avaliacao`, `06-implantacao` | Confronto com critérios de aceite, cenários financeiros, classificador de segmento para reservas novas |
| `0ea3d53` | 23/10 | Paulo Everton | `05-avaliacao`, `06-implantacao` | Polimento: resumo executivo, limpeza de variáveis |
| `49c1f83` | 30/10 | Luan Victor | `07_modelagem_bonus` | Notebook bônus de modelagem (1.787 linhas adicionadas) |
| `(commit atual)` | 30/10 | Paulo Everton | `docs/relatorio-final.md` (novo) | Redação do relatório final consolidado |
| `(commit atual)` | 30/10 | Paulo Everton | `docs/avaliacao.md`, `docs/modelagem.md`, `docs/entendimento-negocio.md`, `docs/entendimento-dados.md`, `docs/fonte-dados.md`, `docs/criterios-sucesso.md`, `docs/analise-exploratoria.md`, `docs/implementacao.md`, `docs/preparacao.md`, `docs/status.md`, `README.md`, `mkdocs.yml` | Revisão e atualização de toda a documentação do site |

### Síntese de contribuição por pessoa

- **Luan Victor Gurgel Quintela:** autor do núcleo da clusterização
  (`04-0`/`04-1`, escolha de k e treino do K-Means), da limpeza inicial dos
  dados (`02-ajustes-dados`), da adaptação dos notebooks de EDA ao domínio de
  hotelaria, e do notebook bônus (`07`).
- **Paulo Everton Souza Rodrigues:** autor do scaffold do projeto, da EDA
  original (`00`, `01`), da preparação (`03`), da redução de dimensionalidade
  e regras de associação (`04-2`, `04-3`), do Canvas do Problema, da
  avaliação e implantação (`05`, `06`), e de toda a consolidação de
  documentação, incluindo o relatório final.

### Autoavaliação individual

A tabela acima é evidência objetiva (commits), não substitui a autoavaliação
em si. Os campos abaixo são **rascunhos derivados apenas dessa evidência**, cada integrante deve revisar, corrigir e completar a própria seção em
primeira pessoa antes da entrega; o que está aqui não é uma declaração real
da pessoa.

#### Paulo Everton Souza Rodrigues

- **O que fiz:** estruturei o repositório a partir do template, criei os
  notebooks de dicionário de dados e EDA, montei a preparação dos dados
  (encoding e padronização), fiz a redução de dimensionalidade (PCA/UMAP) e
  as regras de associação, preenchi o Canvas do Problema, conduzi a
  avaliação contra os critérios de aceite, implantei o classificador de
  segmento para reservas novas e consolidei toda a documentação do site,
  incluindo o relatório final.
- **O que aprendi de novo:** a diferença prática entre separação geométrica
  (silhueta) e separação que importa para o negócio. A silhueta ficou abaixo
  da meta (0,124 contra 0,25), mas quando cruzei os segmentos com
  cancelamento a diferença foi de 6,6 vezes, com significância estatística, aprendi que um critério técnico "não atendido" não significa que o
  resultado não serve, e que é preciso ter os dois tipos de critério
  (técnico e de negócio) definidos antes de modelar, não depois, para não
  ficar refém de um número só.
- **O que faria diferente:** eu teria centralizado as constantes de critério
  (silhueta mínima, faixa de k, teto de ruído) em um `src/config.py` desde o
  início, em vez de deixar espalhadas pelos notebooks, isso já está
  registrado como lacuna no `05-avaliacao`. Também teria fotografado o
  Quadro PBL em cada encontro, em vez de deixar um rascunho em texto para
  substituir depois; isso virou uma pendência que deveria ter sido resolvida
  no processo, não ao final.
- **Nota de autoavaliação:** 8/10. Entreguei a maior parte da cadeia tratável
  sozinho (EDA, preparação, redução de dimensionalidade, associação,
  avaliação, implantação e documentação) com resultados que se sustentam
  tecnicamente e fazem sentido de negócio, mas deixei passar dois itens de
  entrega explícitos do PBL (pitch executivo e registro fotográfico dos
  quadros) que não dependiam de dado nenhum, só de organização, por isso
  não é 9 ou 10.

#### Luan Victor Gurgel Quintela

- **O que fiz (evidência objetiva):** limpeza e ajuste inicial dos dados,
  adaptação dos notebooks de dicionário/EDA ao domínio de hotelaria, decisão
  do número de grupos e treino da clusterização (K-Means, k=5), e notebook
  bônus de modelagem.
- **O que aprendi de novo:** `TODO, preencher em primeira pessoa`
- **O que faria diferente:** `TODO, preencher em primeira pessoa`
- **Nota de autoavaliação (0-10) e justificativa:** `TODO, preencher em primeira pessoa`

!!! warning "Pendência"
    A divisão de trabalho acima é rastreável e verificável (commits reais,
    não suposição). O que falta de fato é subjetivo e só cada pessoa pode
    escrever: o que aprendeu, o que faria diferente, e a nota com
    justificativa. Falta ainda, fora deste documento, o registro fotográfico
    dos cinco Quadros PBL físicos (ver pendência já registrada em "O que foi
    esquecido", acima).

## Próximas etapas

Decisão sobre o destino do projeto, com base nas duas seções anteriores.

!!! info "Decisão"
    - [x] **Implantar**, com a ressalva da silhueta registrada; seguir para [Implementação](implementacao.md)
    - [ ] **Iterar**: voltar a uma fase anterior; indique qual e por quê
    - [ ] **Encerrar**: registrar o aprendizado e não prosseguir

| Ação | Responsável | Prazo |
|:---|:---|:---|
| Expor o classificador de segmento para reservas novas (`06-implantacao.ipynb`) | equipe técnica | feito |
| Validar as premissas financeiras (receita, ganho, custo) com a operação | equipe de negócio | antes do go-live |
| Rodar a clusterização com e sem duplicatas (lacuna 1) | equipe técnica | próxima iteração |
| Fotografar o Quadro PBL e substituir o rascunho em texto | equipe completa | próximo encontro |
