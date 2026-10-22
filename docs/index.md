# Turismo e Hotelaria

## Introdução

Operamos um resort e um hotel urbano e tratamos as duas operações com a mesma régua de
overbooking, de canal de venda e de antecedência de reserva. Este projeto investiga se as
119.390 reservas registradas entre 2015 e 2017 se organizam naturalmente em perfis
distintos, sem usar o desfecho da reserva (cancelada ou não) para formar esses perfis, e
o que cada perfil deveria receber de política diferenciada.

A base é a *Hotel Booking Demand* (Antonio, de Almeida & Nunes, 2019), pública e de uso
livre (CC BY 4.0). O Tema 02 do curso (Turismo e hotelaria) a escolheu por ser a base
mista mais rica do conjunto: a maioria das colunas é categórica (país, canal, tipo de
quarto), o que torna a escolha de distância e de codificação parte do próprio problema, não
um detalhe técnico.

## Dados do projeto

A entidade de análise é a **reserva**, não o hóspede nem a estadia: é no nível da reserva
que a operação decide overbooking, canal e antecedência. Cada linha da base é uma reserva
feita em um dos dois hotéis, com 32 colunas originais que vão desde o país de origem até o
número de pedidos especiais.

### Levantamento inicial

!!! info "Tipo do projeto"
    - [ ] Análise exploratória
    - [ ] Modelo preditivo
    - [ ] Modelo de classificação
    - [x] Modelo de agrupamento
    - [ ] Detecção de anomalias

### Nível de acesso

!!! warning "Confidencialidade"
    - [x] Público
    - [ ] Interno (toda a organização)
    - [ ] Restrito (apenas a área requisitante)

### Objetivos de negócio

!!! quote ""
    Descobrir quantos perfis de reserva existem na carteira (no máximo cinco, por
    restrição orçamentária), caracterizar cada um em linguagem de negócio e recomendar uma
    ação operacional diferenciada por perfil: política de overbooking, canal de venda
    preferencial e tratamento de antecedência, com uma métrica para acompanhar se a ação
    funcionou.

## Sobre o projeto

### Histórico do documento

| Data       | Versão | Descrição                   | Autor         |
| :--------- | :----- | :--------------------------- | :------------ |
| 17/10/2026 | 1.0    | Preenchimento inicial do Canvas | Paulo Everton Souza Rodrigues |

### Dados do solicitante

``` mermaid
flowchart TD
    id1[Disciplina de Aprendizado Não Supervisionado] --o id2[Equipe Turismo e Hotelaria]
```

| Nome                      | Cargo / Função | E-mail            |
| :------------------------ | :------------- | :---------------- |
| Diretoria (simulada)      | Operação de hotelaria e turismo | N/A |

### Dados da equipe técnica

| Nome               | Cargo / Função        | E-mail            |
| :----------------- | :-------------------- | :---------------- |
| Paulo Everton Souza Rodrigues | Cientista de Dados | peverton.ce@gmail.com |
| Luan Victor Gurgel Quintela   | Cientista de Dados | Luanvictorgurgelquintela@gmail.com |
