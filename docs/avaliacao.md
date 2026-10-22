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
