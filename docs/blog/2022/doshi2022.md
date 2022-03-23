---
description: Neste artigo os autores propõem uma métrica que mede a pontualidade e precisão para modelos de detecção de anomalias em sequências. Além disso também é proposta uma abordagem eficiente para detecção de anomalias em séries temporais baseada em transformers, que foi avaliada extensivamente em vários benchmarks.
tags:
  - reading notes
  - anomaly detection
template: blog.html
---

# Reward Once, Penalize Once: Rectifying Time Series Anomaly Detection

!!! note ""

    Neste artigo os autores propõem uma métrica que mede a pontualidade e precisão para modelos de detecção de anomalias em sequências. Além disso também é proposta uma abordagem eficiente para detecção de anomalias em séries temporais baseada em transformers, que foi avaliada extensivamente em vários benchmarks.


<!-- https://arxiv.org/pdf/2203.05167.pdf -->

<!-- ## :fontawesome-solid-chalkboard-user: Background

### Detecção sequencial de anomalias



Em diversas aplicações é crucial detectar tais sequências em tempo hábil. Controlar o número de falsos alarmes também é crucial para garantir a confiabilidade do sistema de detecção

### Falha da avaliação baseada em instância ajustada -->

## :material-lightbulb: Proposta

### Avaliação de desempenho

#### Average detection delay

Considere uma série temporal $\{X_1, X_2, \dots, X_t, \dots\}$ que pode incluir sequências anômalas começando e terminando em tempos desconhecidos. Denote um tempo desconhecido da $i$-ésima sequência anômala como $\tau_i$ e $T_i$ como o tempo do alarme. A média de atraso de detecção (_average detection delay_, ADD) pode ser calculado como

\begin{equation}
ADD = \frac{1}{S} \sum_{i=1}^S \min \left( T_i - \tau_i, \delta_\max \right),
\end{equation}
em que  $S$ denota o número de eventos anômalos. Uma vez que a maioria das anomalias indica incidentes críticos, pode ser essencial detectar um evento anômalo em um determinado período de tempo. Dito isto, se um alarme não é ligado dentro de uma duração $[ \tau_i, \tau_i + \delta_{\max} ]$ depois que a atividade anômala $i$ ocorrer, em que $\delta_{\max}$ é a máxima tolerância máxima de atraso.

#### Precisão do alarme de sequência

Número de eventos anômalos detectados com respeito ao número total de alarmes. Semelhante à métrica de precisão. No entanto, em contraste com a métrica de precisão baseada em instância, a precisão do alarme de sequência (_Sequence Alarm Precision_, SAD) se concentra na detecção de sequências anômalas verdadeiras e, portanto, se concentra apenas na detecção precisa do início do evento anômalo. Se um alarme é disparado antes mesmo de um evento começar, ou seja, $T_j \leq \tau_i$, então é considerado um alarme falso. A precisão do alarme de sequência pode ser calculado usando

\begin{equation}
P = \frac{1}{\hat{S}} \sum_{j=1}^\hat{S} \mathbf{1}_{\{=\}} T_j \in \bigcup_{i=1}^{S} [ \tau_i, \tau_i + \delta_{\max} ],
\end{equation}

em que  $\mathbf{1}_{\{\cdot\}}$ denota a função indicadora, $\hat{S} = |\{T_j\}|$ é a quantidade total de alarmes e $|\cdot|$ é a cardinalidade de um conjunto.

#### Atraso de precisão de sequência

Atraso de precisão de sequência (_Sequence Precision Delay_, SPD) combina a média de atraso de detecção com a precisão do alarme de sequência. Nesse caso, ela utiliza o valor normalizado da ADD. Desse modo, matematicamente temos

\begin{equation}
\textrm{SPD} = \int_{0}^{1} P(\alpha) d\alpha,
\end{equation}
em que $\alpha$ denota o valor normalizado da média de atraso de detecção (_normalized average detection delay_, NADD)


<!-- ### TiSAT: Time Series Anomaly Transformer



## :fontawesome-solid-vial: Experimentos

### Conjuntos de dados

 - _Server Machine Dataset_ (SMD): conjunto de dados coletado por uma grande companhia de internet em 5 semanas com 38 dimensões.
 - _Pooled Server Metrics_ (PSM): proposto pelo eBay e consiste em dados com 26 dimensões, capturados internamente dos seus servidores.
 - _Mars Science Laboratory rover_ (MSL) e  _Soil Moisture Active Passive satellite_ (SMAP): conjuntos de dados da NASA de telemetria e e anomalias. 55 e 25 dimensões, respectivamente. Na sua maioria os dados são categóricos. Somente os dados de telemetria foram utilizados.
 - SWaT: coletado em uma industria. As variáveis são coletadas de uma estação de tratamento de esgoto. O conjunto de dados foi coletado em uma semana e consiste em 51 dimensões, em que as anomalias foram causadas por um ataque cibernético.

 Cada conjunto de dados inclui subconjuntos de treino, validação e teste. Anomalias são somente rotuladas no conjunto de teste.
