---
title: 'Contextualized: Heterogeneous Modeling Toolbox'
tags:
  - Python
  - Machine Learning
  - Modeling
  - Heterogeneous
  - Context
authors:
  - name: Caleb N. Ellington
    orcid: 0000-0001-7029-8023
    corresponding: true
    equal-contrib: true
    affiliation: "1"
  - name: Benjamin J. Lengerich
    orcid: 0000-0001-8690-9554
    corresponding: true
    equal-contrib: true
    affiliation: "2, 3"
  - name: Wesley Lo
    affiliation: "2, 4"
  - name: Aaron Alvarez
    affiliation: "5"
  - name: Andrea Rubbi
    affiliation: "6"
  - name: Manolis Kellis
    affiliation: "2, 3"
  - name: Eric P. Xing
    affiliation: "1, 7"
affiliations:
 - name: Carnegie Mellon University, USA
   index: 1
 - name: Massachusetts Institute of Technology, USA
   index: 2
 - name: Broad Institute of MIT and Harvard, USA
   index: 3
 - name: Worcester Polytechnic Institute, USA
   index: 4
 - name: University of Cincinnati, USA
   index: 5
 - name: Cambridge University, UK
   index: 6
 - name: Mohamed bin Zayed University of Artificial Intelligence, UAE
   index: 7
 - name: Petuum Inc., USA
   index: 8
date: 26 Jan 2024
bibliography: paper.bib
---


![](figs/contextualized_logo.png){width=90%}


# Summary
Heterogeneous and context-dependent systems are common in real-world processes, such as those in biology, medicine, finance, and the social sciences. 
However, learning accurate and interpretable models of these heterogeneous systems remains an unsolved problem. 
Most statistical modeling approaches make strict assumptions about data homogeneity, leading to inaccurate models, while more flexible approaches are often too complex to interpret directly.
Fundamentally, existing modeling tools force users to choose between accuracy and interpretability.
Recent work on Contextualized Machine Learning [@lengerich_contextualized_2023] has introduced a new paradigm for modeling heterogeneous and context-dependent systems, which uses contextual metadata to generate sample-specific models, providing context-specific model-based insights and representing data heterogeneity with context-dependent model parameters.

Here, we present [`Contextualized`](https://contextualized.ml/), a SKLearn-style Python package for estimating and analyzing personalized context-dependent models based on Contextualized Machine Learning.
`Contextualized` implements two reusable and extensible concepts: *a context encoder* which translates sample context or metadata into model parameters, and *sample-specific model* which is defined by the context-specific parameters.
With the flexibility of context-dependent parameters, each context-specific model can be a simple model class, such as a linear or Gaussian model, providing direct model-based interpretability without sacrificing overall accuracy.

# Statement of Need

“Personalized modeling” is a statistical method that has started to gain popularity in recent years for representing complex and heterogeneous systems exhibiting individual, sample-specific effects, such as those prevalent in complex diseases. 
In its basic form: $x_i \sim P(X_i ;θ_i)$, where $i$ indexes a sample, $θ_i$ is the parameters defining the sample-specific distribution, and $x_i$ corresponds to the  observation drawn from this sample-specific distribution, where understanding sample heterogeneity is equivalent to estimating data distributions with sample-specific parameters. 
Disparate lineages of research have made contributions to this problem, typically by imposing a constraint on the variation of model parameters across all subjects [@hastie1993varying; @fan_statistical_1999; @wang_bayesian_2022; @kolar_estimating_2010; @parikh_treegl_2011; @kuijjer_estimating_2019]. 
However, prior methods only model variation for a few continuous covariates [@hastie1993varying; @fan_statistical_1999; @wang_bayesian_2022] or groups [@kolar_estimating_2010; @parikh_treegl_2011], or otherwise employ sample-left-out methods and are statistically underpowered [@kuijjer_estimating_2019]. 
Recently, contextual explanation networks reframed the sample-specific parameter estimation problem as a more flexible and generalizable latent variable inference problem that can naturally leverage any additional contextual data pertinent to the study subjects [@al-shedivat_contextual_2020]. 
This was then generalized to a new analytical paradigm, Contextualized Machine Learning, which provides a unified mathematical framework for inferring and estimating personalized models of heterogeneous and context-dependent systems [@lengerich_contextualized_2023].

Formally, given subject data $X = \{X_i\}_{i=1}^N$ and context data $C = \{C_i\}_{i=1}^N$ where $i$ indexes subjects, each observed only once, we can express the likelihood of all data in the form of 
$$P(X,C) \propto \int_{\theta} d\theta P_M (X \mid \theta) P ( \theta \mid C)\,$
where we call $P ( \theta \mid C)$ the context encoder, and $P_M (X \mid \theta)$ the sample-specific model of model class $M$. 
So long as the choice for both the context encoder and sample-specific model are differentiable, we can learn to estimate parameters $Θ_i$ for each subject $i$ via end-to-end backpropagation with gradient-based algorithms 
such that $P(X \mid C)$ is maximized.
Conveniently, $C$ can contain any multivariate or real features that are relevant to the study, such as clinical, genetic, textual, or image data, and the context encoder can be any differentiable function, such as a neural network, that maps $C_i$ to $Θ_i$.

`Contextualized` implements this framework for key types of context encoders and sample-specific models, opening up new avenues for quantitative analysis of complex and heterogeeneous data, and simplifying the process of transforming this data into results with plug-and-play analysis tools. In particular, `Contextualized`:

1. **Unifies Past Modeling Frameworks:** `Contextualized` unifies modeling approaches for both homogeneous and heterogeneous data, including population, sub-population, (latent) mixture, cluster-based, time-varying [@kolar_estimating_2010], tree-varying [@parikh_treegl_2011], and varying-coefficient models [@hastie1993varying]. 
Additionally, `Contextualized` naturally falls back to these more traditional modeling frameworks when complex heterogeneity is not present.
Not only is this convenient, but it limits the number of modeling decisions and validation tests required by users, reducing the risk of misspecification and false discoveries [@lengerich_contextualized_2023].
2. **Models High-resolution Heterogeneity:** Contextualized models adapt to the context of each sample by using a context encoder, naturally accounting for high-dimensional, continuous, and fine-grained variation between samples [@ellington_contextualized_2023].
3. **Quantifies Heterogeneity in Data:** Context-specific models quantify the randomness and structure of the systems underlying each data point, and variation in context-specific model parameters quantifies the heterogeneity between data points [@deuschel_contextualized_2023].
`Contextualized` provides tools to analyze, test, and validate contextualized models, unlocking new studies of structured heterogeneity.
4. **Interpolates and Extrapolates to Unseen Contexts:** By using context encoders to translate between contextual information and model parameters, `Contextualized` learns meta-relationships between metadata and data. At test time, `Contextualized` can adapt to contexts which were never observed in the training data [@ellington_contextualized_2023].
5. **Analyzes Latent Processes:** By associating structured models with each sample, `Contextualized` enables analysis of samples with latent processes.
These latent processes can be inferred from patterns in context-specific models, and can be used to identify latent subgroups, latent trajectories, and latent features that explain heterogeneity [@lengerich_discriminative_2020].
6. **Provides Direct Interpretability:** `Contextualized` estimates and analyzes context-specific statistical models. 
These statistical models are mathematically-constrained such that each parameter has specific meaning, permitting direct interpretation and immediate results [@lengerich_automated_2022].
7. **Incorporates Multi-modal Data:** Context is a general and flexible concept, and context-encoders can be used to instill any type of contextual information into contextualized models, including images, text, tabular data, and more [@lengerich_discriminative_2020; @lengerich_notmad_2021; @al-shedivat_contextual_2020; @stoica_contextual_2020].
8. **Enables Modular Development:** The context encoder and sample-specific model within `Contextualized` are both highly adaptable; the context encoder can be replaced with any differentiable function, and any statistical model with a differentiable likelihood or log-likelihood can be contextualized and made sample-specific, benefiting from a rich ecosystem of statistical models and deep learning methods.

# Usage

The `Contextualized` software is structured through three primary resources:

1. A simple plug-and-play interface to learn contextualized versions of popular model classes (e.g. classifiers, linear regression, graphical models, Gaussians).
2. A suite of context encoders to incorporate any modality of contextual data (e.g. continuous, categorical, images, text) and/or impose restrictions on context-dependent relationships (e.g. feature independence, interaction effects).
3. Intuitive analysis tools to understand, quantify, test, and visualize data with heterogeneous and context-dependent behavior. 
These tools focus on visualizing heterogeneity, automatic hypothesis testing, and feature selection for context-dependent and context-invariant features.

Installation instructions, tutorials, API reference, and open-source code are all available at [contextualized.ml](https://contextualized.ml).


# Acknowledgements

We are grateful for early user input from Juwayni Lucman, Alyssa Lee, and Jannik Deuschel.


# References