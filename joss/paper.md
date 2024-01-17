---
title: 'Contextualized ML: Heterogeneous Modeling Toolbox'
tags:
  - Python
  - machine learning
  - heterogeneous effects
authors:
  - name: Caleb Ellington
    orcid: 0000-0001-7029-8023
    equal-contrib: true
    affiliation: 1
  - name: Ben Lengerich
    orcid: 0000-0001-8690-9554
    equal-contrib: true
    affiliation: "2, 3"
  - name: Wesley Lo
    affiliation: "2, 4"
  - name: Aaron Alvarez
  - name: Andrea Rubbi
    affiliation: "5"
  - name: Manolis Kellis
    orcid: 0000-0000-0000-0000
    equal-contrib: false
    affiliation: "2, 3"
  - name: Eric P. Xing
    corresponding: true
    affiliation: "1, 6"
affiliations:
 - name: Carnegie Mellon University, USA
   index: 1
 - name: Massachusetts Institute of Technology, USA
   index: 2
 - name: Broad Institute of MIT and Harvard, USA
   index: 3
 - name: Worcester Polytechnic Institute, USA
   index: 4
 - name: Cambridge University, UK
   index: 5
 - name: Mohamed bin Zayed University of Artificial Intelligence, UAE
   index: 6
date: 1 Jan 2024
bibliography: paper.bib
---


![](figs/contextualized_logo.png){width=90%}


# Summary

Heterogeneous and context-dependent systems are a defining characteristic of observational data arising from real-world processes, such as in biology, medicine, finance, and the social sciences. 
However, heterogeneous and context-dependent systems are not well-characterized by homogeneous models; as a result, existing tools have pushed users to adopt an accuracy vs interpretability trade-off. 
Accurate models capable of flexibly adapting to context-dependent effects do not reveal these effects to users, and interpretable models are often too restricted to capture context-dependent effects.

To overcome this tradeoff, we present [`Contextualized ML`](https://contextualized.ml/), an easy-to-use SKLearn-style toolbox for estimating and analyzing context-dependent models.
`Contextualized ML` uses contextual metadata to generate sample-specific models, providing context-specific model-based insights and representing data heterogeneity with context-dependent model parameters.
With the flexibility of context-dependent effects, each context-specific model can be a simple linear model without sacrificing overall model accuracy.

`Contextualized ML` introduces two reusable concepts: *a context encoder* which translates sample context or metadata into model parameters, and *sample-specific model* which is defined by the context-specific parameters (\autoref{fig:paradigm}).
Our formulation unifies a wide variety of popular modeling approaches, including population modeling, sub-population modeling, (latent) mixture modeling, cluster modeling, time-varying models, and varying-coefficient models [@hastie1993varying], and falls back to these traditional models when complex heterogeneity is not present.
Finally, by explicitly modeling the metadata--parameter relationships, `Contextualized ML` enables interpolation and extrapolation to unseen values of contextual metadata.

`Contextualized ML` is a lean, utility-oriented implementation of the broader Contextualized Machine Learning paradigm [@lengerich_contextualized_2023], focusing on novel and popular use cases from recent works developing contextualized models [@ellington_contextualized_2023; @deuschel_contextualized_2023; @lengerich_notmad_2021; @al-shedivat_contextual_2020; @lengerich_automated_2022; @lengerich_discriminative_2020; @al-shedivat_personalized_2018; @stoica_contextual_2020].
We provide `Contextualized ML` as a Python package written in native PyTorch with a simple SKLearn-style interface.

**Contextualized ML serves three primary purposes:**

1. A simple plug-and-play interface to learn contextualized versions of popular model classes (e.g. linear regression, classifiers, graphical models, Gaussians).
2. Intuitive analysis tools to understand, quantify, test, and visualize data with heterogeneous and context-dependent behavior.
3. An extensible and modular framework for researchers to develop new contextualized models.

Installation instructions, tutorials, API reference, and open-source code are available at [contextualized.ml](https://contextualized.ml).


# Benefits and Use Cases of Contextualized ML

![Contextualized Machine Learning paradigm.\label{fig:paradigm}](figs/context_encoders_sideways.pdf){width=100%}

The `Contextualized ML` framework (\autoref{fig:paradigm}) exhibits desirable properties, such as its ability to (1) infer context-specific models without losing power by partitioning data, (2) incorporate multiple data modalities via context encoding, (3) test the strength of heterogeneity in data, and (4) automatically default to the most appropriate type of traditional model when complex heterogeneity is not present.

## One-stop Shop
The `Contextualized ML` framework unifies many popular modeling approaches, including population modeling, sub-population modeling, (latent) mixture modeling, cluster modeling, time-varying models, and varying-coefficient models.
`Contextualized ML` further supercedes these methods, permitting even sample-specific modeling without losing statistical power.
When complex types of heterogeneity are not present, the `Contextualized ML` framework naturally defaults to the most appropriate type of traditional model, which can quickly be tested and validated with the analysis tools.
Not only is this convenient, but it limits the number of modeling decisions and statistical tests required by users, reducing the risk of false discoveries.

## High-resolution Heterogeneity
While real-world heterogeneity is often characterized by continuous, high-dimensional, and fine-grained variation, most existing methods focus on a limited type of heterogeneity, described as the presence of multiple distinct subgroups within a population.
Cluster-based, mixture-based, cohort-based, and subpopulation-based methods are designed to recover this and only this type of heterogeneity, inferring a single statistical model that is shared amongst all the samples in a (sub)group, implicitly assuming that intra-cluster or intra-cohort samples are homogeneous and identically distributed.
This simplifies the resulting mathematical models, but ignores most heterogeneity -- as a result, models which use homogeneous effects to mimic heterogeneous phenomena force users to pick *either* model flexibility or parsimony.

In contrast, *contextualized* models adapt to the context of each sample by using a context encoder, naturally accounting for high-dimensional, continuous, and fine-grained variation over contexts.
By embracing heterogeneity and context-dependence, contextualized learning provides high-resolution sample-specific predictions while retaining the glass-box nature of statistical modeling.

## Context Interpolation and Extrapolation
By learning to translate between contextual information and model parameters, `Contextualized ML` learns about the meta-relationships of metadata--data. 
At test time, `Contextualized ML` can adapt to contexts which where never observed in the training data, either by interpolating between observed contexts or extrapolating to new domain of context.

To understand the significance of this, it is helpful to consider that, traditionally, contextual factors would be controlled for by splitting data into many context-specific groups. 
Not only does this quickly limit statistical power and model accuracy as the number of contexts increases, but in real data the number of possible contexts can vastly exceed the amount of data available.
For example, if we wished to model human development and disease in the context of human genetics, there are 11,500,000 known single-nucleotide polymorphisms in humans, implying $2^{11,500,000}$ possible genetic contexts, but only $2^{37}$ people have ever existed.
Observing every possible context, much less drawing sufficient samples for each context, is impossible.

`Contextualized ML` enables context-specific modeling even when the number of contexts vastly exceeds the number of observed samples by learning to interpolate and extrapolate between contexts.
A recent study applies this property, contextualizing models of gene regulation in cancer with 1600 patient-specific genetic variants, learning to interpolate between $2^{1600}$ possible contexts with only $2^{13}$ samples [@ellington_contextualized_2023].

## Interpretability
`Contextualized ML` estimates and analyzes context-specific statistical models. 
Such statistical models are designed for each parameter to have specific meaning.
In contrast to post-hoc interpretation of deep learning methods, which rely on function approximation and hence can be fickle and unreliable [@deuschel_contextualized_2023], contextualized models are directly estimated from data. 
As a result, interpretations of contextualized models are robust, mathematically constrained, and offer as many interpretation mechanisms as there are available types of statistical models.

## Quantifying Heterogeneity
Sample-specific statistical models quantify the randomness and the structure of the systems underlying each data point.
By comparing the sample-specific models, we can quantify how these systems vary to produce heterogeneity in data, identify the most important sources of heterogeneity, and determine what heterogeneity is explainable and what is random noise.
`Contextualized ML` provides tools to analyze contextualized models, focusing on visualizing heterogeneity, automatic hypothesis testing, and feature selection for context-dependent and context-invariant features.

## Analysis of Latent Processes
By associating structured models with each sample, `Contexutalized ML` enables analysis of samples with latent processes.
These latent processes can be inferred from patterns in context-specific models, and can be used to identify latent subgroups, latent trajectories, and latent features.
A classic example is the analysis of time-varying processes, but `Contextualized ML` extends this to any type of latent variation.

## Naturally Accounting for Multi-modal Information
Context is a general and flexible concept, and context-encoders can be used to incorporate any type of contextual information for contextualized modeling.
`Contextualized ML` naturally allows multi-modal and multi-source information to be used for contextualized modeling.
In prior works, this has included images [@al-shedivat_contextual_2020; @lengerich_discriminative_2020], text [@stoica_contextual_2020], genetic data [@ellington_contextualized_2023], clinical data [@al-shedivat_personalized_2018; @lengerich_notmad_2021; @lengerich_automated_2022], and actions of an agent [@deuschel_contextualized_2023].

## Modularity and Extensibility
The `Contextualized ML` framework is based on two reusable concepts: the *context encoder* which translates sample context or metadata into model parameters, and the *sample-specific model* which is defined by the context-specific parameters.
Both components are highly adaptable; the context encoder can be replaced with any differentiable function, and any statistical model with a differentiable likelihood or log-likelihood can be contextualized and made sample-specific.

In the past, this extensibility has been leveraged to enforce feature independence in the context encoder [@lengerich_automated_2022], constrain context-specific models to be within a set of archetypal models [@al-shedivat_contextual_2020], and introduce new types of contextualized models [@lengerich_notmad_2021; @ellington_contextualized_2023].


# Acknowledgements

We are grateful for early user input from Juwayni Lucman, Alyssa Lee, and Jannik Deuschel.


# References