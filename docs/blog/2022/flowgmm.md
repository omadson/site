---
description: Neste artigo, os autores desenvolveram o Flow Gaussian Mixture Model (FlowGMM), uma alternativa ponta-a-ponta para aprendizagem generativa semi-supervisionada através de normalizing flows, utilizando um modelo latente de mistura de gaussianas.
tags:
  - reading notes
template: blog.html
---

# Semi-Supervised Learning with Normalizing Flows

<!-- ![](images/flow-gmm/illustration.png) -->

!!! note ""

    Neste artigo, os autores desenvolveram o Flow Gaussian Mixture Model (FlowGMM), uma alternativa ponta-a-ponta para aprendizagem generativa semi-supervisionada através de normalizing flows, utilizando um modelo latente de mistura de gaussianas.

[![](https://img.shields.io/badge/download-pdf-orange)](http://proceedings.mlr.press/v119/izmailov20a/izmailov20a.pdf){target=_blank}
![](https://img.shields.io/badge/pmlr-2020-blue)


<!-- ## Contribuições

 - Descrição de uma nova estrutura para aprendizagem semi supervisionada com modelos generativos, empregando estimadores de densidade paramétricos ricos, formados pela fusão de modelagem probabilística e redes neurais profundas.
 -  É apresentado como a inferência variacional pode ser aplicada ao problema da classificação semis supervisionada. Em particular, é desenvolvido um algoritmo de inferência variacional estocástica que permite a otimização conjunta do modelo e dos parâmetros variacionais, e que é escalonável para grandes conjuntos de dados.
 -  Resultados qualitativos de que modelos semis supervisionados  generativos aprendem a separar as classes de dados das variabilidades intraclasse, permitindo de uma maneira muito direta simular analogias de imagens em uma variedade de conjuntos de dados. -->


## :fontawesome-solid-chalkboard-user: Background

### Normalizing Flows

_Normalizing Flows_ (NF) são modelos não supervisionados para estimativa de densidade de probabilidade definidos através de um mapeamento inversível $f_{\theta}:\mathcal{X} \to \mathcal{Z}$ do espaço de dados $\mathcal{X}$ para um espaço latente $\mathcal{Z}$. Deste modo, a distribuição dos dados pode ser modelada através da transformação $f^{-1}_{\theta}:\mathcal{Z} \to \mathcal{X}$ aplicada a uma variável aleatória das distribuições latentes $z \sim p_{\mathcal{Z}}$. Aplicando a regra da mudança de variáveis, obtemos a distribuição de densidade da variável aleatória observada, dada por

\begin{equation}
p_{\mathcal{X}}(x) = p_{\mathcal{Z}}(z)\left(f_{\theta}(x)\right) \left| \det \left( \frac{\partial f}{\partial x }\right) \right|
\label{eq:nf}
\end{equation}
O mapeamento $f_{\theta}$ pode ser implementado através de uma cadeia de funções inversíveis, parametrizadas por uma arquitetura de redes neurais, construída para garantir invertibilidade e eficiência no computo das log-determinantes. O modelo pode ser treinado a partir da maximização da verossimilhança da equação (\ref{eq:nf}) de dados de treinamento com respeito aos parâmetros $\theta$.

## :material-lightbulb: Proposta

### Flow Gaussian Mixture Model (FlowGMM)

Considere um conjunto de dados rotulados $\mathcal{D}_u = \{\boldsymbol{x}_n\}_{n=1}^N$ e um conjunto de dados não rotulados $\mathcal{D}_\ell = \{(\boldsymbol{x}_m, y_m)\}_{m=1}^M$, tal que $y_m \in \mathcal{C}$. O FlowGMM, utiliza a seguinte distribuição para o espaço latente de $\mathcal{D}_\ell$

\begin{equation}
    p_{\mathcal{Z}}(z|y=k) = \mathcal{N}(z|\mu_k, \Sigma_k). \label{eq:conditional-probability}
\end{equation}
em que $\mu_k$ e $\Sigma_k$ são, respectivamente, a média e a matriz de covariância da classe $k$.

A distribuição marginal de $z$ é então uma mistura de gaussianas. Com classes balanceadas, esta distribuição é

\begin{equation}
    p_{\mathcal{Z}}(z) = \frac{1}{C} \sum_{k=1}^{C} \mathcal{N}\left(z|\mu_k, \Sigma_k\right).
\end{equation}
em que $C$ é o número de classes.

Combinando as equações (\ref{eq:conditional-probability}) e (\ref{eq:nf}), a verossimilhança do conjunto de dados $\mathcal{D}_\ell$ é

\begin{equation}
    p_{\mathcal{X}}(x|y=k) = \mathcal{N}\left(f_\theta(x)|\mu_k, \Sigma_k\right) \left| \det \left( \frac{\partial f}{\partial x }\right) \right|,
\end{equation}
e a verossimilhança dos dados não rotulados é $p_{\mathcal{X}}(x) = \sum_k p_{\mathcal{X}}(x|y=k)p(y=k)$.

O modelo pode ser treinado de uma maneira semi supervisionada afim de maximizar a verossimilhança conjunta dos dados rotulados e não rotulados

\begin{equation}
    p_{\mathcal{X}}(\mathcal{D}_u, \mathcal{D}_\ell |\theta) = \prod_{x_n \in \mathcal{D}_u} p_{\mathcal{X}}(x_n) \prod_{(x_m, y_m) \in \mathcal{D}_\ell} p_{\mathcal{X}}(x_m, y_m),\label{eq:log-verossimilhanca}
\end{equation}

sobre os parâmetros $\theta$ da função bijetiva $f_\theta$.

Em particular, dado um ponto de teste $x$, uma predição $\hat{y}$ da classe relacionada pode ser realizada utilizando a máxima verossimilhança da densidade condicional em relação as classes, ou seja, $\hat{y} = \arg \max_{k \in \{ 1, \dots, C \} } p_{\mathcal{X}}(y |x )$, em que
\begin{equation}
    p_{\mathcal{X}}(y |x ) = \frac{\mathcal{N}\left(f_\theta(x)|\mu_k, \Sigma_k\right)}{ \sum_{k=1}^C \mathcal{N}\left(f_\theta(x)|\mu_k, \Sigma_k\right)}.
\end{equation}

#### Regularização de consistência

A regularização de consistência penaliza mudanças nas predições da rede com respeito a perturbações na entrada, tal como translação e rotação, com uma perda adicional que pode ser computada para dados não rotulados

\begin{equation}
    \ell_{\text{cons}}(x) = \left\| g\left(x^{(a)}\right) - g\left(x^{(b)}\right) \right\|
\end{equation}
em que $x^{(a)}$ e $x^{(b)}$ são perturbações aleatórias de $x$ e $g$ é o vetor de probabilidades de cada uma das classes.

Motivado por tais métodos o FlowGMM introduz uma regularização de consistência para uma dada imagem $x$, baseada na seguinte equação

\begin{align}
    \ell_\text{cons}(x) &= - \log (x^{(a)}|y^{(b)}) \nonumber \\
                        &= - \log \mathcal{N}\left(f_\theta\left(x^{(a)}\right)|\mu_{y^{(b)}}, \Sigma_{y^{(b)}}\right) -  \log \left| \det \left(\frac{\partial f}{\partial x^{(a)}} \right) \right|. \label{eq:flow-cons}
\end{align}

Este termo de perda encoraja o modelo a mapear pequenas perturbações das mesmas entradas não rotuladas para os mesmos componentes da distribuição da mistura de gaussianas no espaço latente. A perda final para FlowGMM-cons é então a soma ponderada da perda de consistência (\ref{eq:flow-cons}) e o negativo da log-verossimilhança dos dados rotulados e não rotulados (\ref{eq:log-verossimilhanca}).
<!--
## :fontawesome-solid-vial: Experimentos

O modelo proposto foi avaliado usando conjuntos de dados de diferentes aplicações, que incluem dados sintéticos de baixa dimensão, dados textuais e tabulares e imagens. Além disso, o modelo também foi testando em conjuntos desbalanceados.



### Conjuntos de dados

###  -->
