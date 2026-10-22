# Quadro PBL

!!! warning "Rascunho, não é a entrega final"
    O PBL pede o **quadro físico fotografado**, não um texto. O conteúdo abaixo é um
    rascunho, montado a partir do que já está no repositório (notebooks `00` a `04-3`),
    para a equipe discutir, ajustar e passar para o quadro de verdade no próximo encontro.
    Substitua esta página pelas fotos reais assim que o quadro for preenchido em sala:
    uma foto por encontro, cinco ao todo.

| Fatos | Hipóteses |
|:---|:---|
| 119.390 reservas, 32 colunas originais, dois hotéis (`City Hotel`, `Resort Hotel`). | O tipo de hotel (`hotel`) é um dos eixos que separa os perfis. |
| Taxa geral de cancelamento: 37%. | Reservas de grupo via agência (`market_segment=Groups`) cancelam mais do que reservas diretas. |
| 27% de linhas idênticas; não há chave única de reserva. | As duplicatas são reservas legítimas e repetidas por acaso, não erro de carga: ainda não verificado. |
| `deposit_type=Non Refund` tem 99,4% de cancelamento, contra 28,4% em `No Deposit`. | Esse padrão é um artefato de como certas agências reportam reservas de grupo, não uma escolha real do hóspede. |
| No `04-2`, 13 das 15 variáveis mais relevantes coincidem entre PCA e UMAP: `hotel`, `deposit_type`, `market_segment`, `country`, `meal`, `customer_type`, `distribution_channel`, tipo de quarto e o bloco de datas de chegada. | Esses 13 atributos bastam para descrever os perfis; os demais (ex. `agent`, já descartado) não agregam. |
| No `04-3`, 523 regras fortes (confiança ≥ 60%, lift ≥ 1,2); `lead_time` baixo associa-se a não cancelamento. | Antecedência de reserva é um bom preditor de risco de cancelamento, útil para a política de overbooking. |

| Questões de aprendizagem | Plano de ação |
|:---|:---|
| Quantos grupos a clusterização (`04-0`, `04-1`) realmente confirma, e com que silhueta? | Aguardar o resultado do `04-0`/`04-1` e comparar com os 13 atributos já identificados como relevantes. |
| As duplicatas distorcem a contagem de algum grupo específico? | Rodar a clusterização com e sem duplicatas e comparar os tamanhos dos grupos, antes de decidir se mantém. |
| Hierárquica/Gower exigem amostra em 119 mil linhas: qual tamanho de amostra preserva os grupos do K-Means? | Testar 2.000 a 5.000 reservas amostradas e comparar a silhueta com a base completa. |
| A regra do `Non Refund` deveria entrar na caracterização dos perfis, mesmo sendo um possível artefato? | Registrar a ressalva no relatório e decidir, com a equipe, se o perfil "alto risco de cancelamento" cita essa regra ou não. |

**Regra do quadro:** uma hipótese só sai da coluna 2 quando houver evidência nos dados que
a confirme (vira Fato) ou que a refute (vira nota de rodapé no relatório). Nenhuma hipótese
é apagada, mesmo derrubada.
