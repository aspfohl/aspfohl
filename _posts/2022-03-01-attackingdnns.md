---
title: "Attacking DNNs with Adversarial Examples"
excerpt: "Adversarial examples attack DNNs with small perturbations of the input data that result in significantly different model output. This article will give examples of attacks and strategies for defending against them."
date: 2022-03-01 00:00:00
cover-img: /assets/img/attackingdnns/header.png
thumbnail-img: /assets/img/attackingdnns/thumbnail.png
share-img: /assets/img/attackingdnns/thumbnail.png
published: true
tags: 
  - python
  - pytorch
  - machine-learning
  - security
---
Adversarial examples attack deep neural networks (DNNs) by providing small, undetectable perturbations of the input data that result in significantly different model outputs. There are two types of adversarial examples:
* **White box attacks:** attacker has access to neural network parameters and can backpropagate through the network
* **Black box attacks:** attacker does not have access to neural network parameters

This article will give detailed examples of each type of attack, their applications, and strategies for defending against these attacks.

## Examples of white box attacks

<figure>
  <img src="{{ 'assets/img/attackingdnns/panda.png' | relative_url }}" width="550" height="200" class="center">
  <p style='text-align: center'>Classic adversarial example from Goodfellow2014</p>
</figure> 

How to find an adversarial example:
1. Choose an image _x_ from the testing set
2. Define an adversary _x'_
3. Propagate through the network, minimizing distance between _x_ and _x'_ and/or magnitude of the adversarial noise (usually _x'-x_)

For example, [Athalye et al. Synthesizing Robust Adversarial Examples. 2018](https://arxiv.org/abs/1707.07397) proposes a robust gradient technique that calculates the adversarial example using the infinity norm
<img src="{{ 'assets/img/attackingdnns/equation.png' | relative_url }}" class="center">

<figure>
  <img src="{{ 'assets/img/attackingdnns/cheeseburger.png' | relative_url }}" width="600" height="200" class="center">
  <p style='text-align: center'>Fooling ResNet18 with learned noise, see <a href="https://github.com/aspfohl/hackingdnns/blob/main/hackingdnns/attacks/adversary.py">my script</a></p>
</figure> 

Common and historical gradient-based algorithms for defining adversarial images:
* Fast Gradient Sign Method (FGSM), see [Goodfellow et al. Explaining and Harnessing Adversarial Examples. 2014](https://arxiv.org/abs/1412.6572)
* Box-constrained LBFGS, see [Carlini et al. Towards Evaluating the Robustness of Neural Networks. 2016](https://arxiv.org/abs/1608.04644)
* Iterative FGSM, see [Kurakin et al. Adversarial Machine Learning at Scale. 2017](https://arxiv.org/abs/1611.01236)

**Universal Adversarial Perturbations:** [Moosavi-Dezfooli et al. 2017](https://arxiv.org/abs/1610.08401)
This attack uses a single perturbation vector that is image agnostic and generalizes well across neural network architectures. 

 <figure>
  <img src="{{ 'assets/img/attackingdnns/universal.png' | relative_url }}" width="400" height="300" class="center">
  <p style='text-align: center'>Scaled perturbation vectors for different models trained on ImageNet</p>
</figure> 

**One Pixel Attack:** [Su et al. One Pixel Attack for Fooling Deep Neural Networks, 2019](https://arxiv.org/abs/1710.08864) 
This attack is more limited, where only one pixel is modified in the adversarial example. One pixel attacks are considered semi-black box since the algorithm it uses (differential evolution) only needs the probability of the labels and not the model's entire set of parameters.

<figure>
  <img src="{{ 'assets/img/attackingdnns/one-pixel.png' | relative_url }}" width="350" height="400" class="center"> 
  <p style='text-align: center'>Single pixel (circled) attacks</p>
</figure> 

## Examples of black box attacks

Black box attacks can be harder to expertly deploy, but are still a considerable threat even with limited information.

In [Ilyas et al. Black-box Adversarial Attacks with Limited Queries and Information. 2018](https://proceedings.mlr.press/v80/ilyas18a.html), three threat models with query-limiting, partial-information setting, and label-only setting are proposed.

<figure>
  <img src="{{ 'assets/img/attackingdnns/bb2018.png' | relative_url }}" width="350" height="200" class="center"> 
  <p style='text-align: center'>Example constrained black box attacks</p>
</figure> 

In [Cheng et al. Improving Black-box Adversarial Attacks with a Transfer-based Prior. 2019](https://arxiv.org/abs/1906.06919) transfer-based priors and limited inference queries to the existing model lead to successful black box attacks.

<figure>
  <img src="{{ 'assets/img/attackingdnns/bb2019.png' | relative_url }}" width="400" height="200" class="center"> 
  <p style='text-align: center'>Example attacks vs. number of queries</p>
</figure> 

## Security Implications

Adversarial attacks can be used to compromise the security of any real-world application of DNNs in several ways.

**Physical endangerment**: One of the most popular examples from [Eykholt et al. Robust Physical-World Attacks on Deep Learning Models. 2018](https://arxiv.org/abs/1707.08945) incudes attaching physical perturbations to real world objects to trick machine learning systems. In their paper they attach stickers to stop signs that trick self-driving models into identifying the stop sign as a speed limit sign. 

<figure>
  <img src="{{ 'assets/img/attackingdnns/stop.png' | relative_url }}" width="350" height="200" class="center"> 
  <!-- <p style='text-align: center'>Single pixel (circled) attacks</p> -->
</figure> 

**Dodging, Evasion, & Impersonation**: [Sharif et al's A General Framework for Adversarial Examples with Objectives (2017)](https://arxiv.org/abs/1801.00349) gives examples of how to design physical objects that can be used to thwart face detection and even impersonate other people.

<figure>
  <img src="{{ 'assets/img/attackingdnns/eyeglasses.png' | relative_url }}" width="150" height="300" class="center"> 
  <p style='text-align: center'>Both images were classified by VGG10 as Brad Pitt</p>
</figure> 

**Fraud & scams**: While computer vision is one of the most common areas of research for adversarial attacks, natural language processing has also been shown to be susceptible to these attacks. Consequences of these systems could be thwarting spam filters or influencing models used for credit scoring.

<figure>
  <img src="{{ 'assets/img/attackingdnns/text.png' | relative_url }}" width="350" height="100" class="center"> 
  <p style='text-align: center'>Example of text attack from <a href="https://arxiv.org/abs/2005.05909">Morris et al. TextAttack: A Framework for Adversarial Attacks, Data Augmentation, and Adversarial Training in NLP. 2020</a></p>
</figure> 

**Model degradation**: Adversarial attacks can be used to attack vulnerabilities of reinforcement learning and other online models to manipulate the models policy and inductive learning. The authors show the model's degradation while learning Pong after being fed adversarial perturbations in [Behzadan et al. Vulnerability of Deep Reinforcement Learning to Policy Induction Attacks. 2017](https://arxiv.org/abs/1701.04143)

## Defending against attacks

**Prevent white box attacks**: Using traditional model architectures (e.g. ResNet50) and widely popular training datasets (e.g. ImageNet) increase the probability that an attacker could launch a white box attack on your model, even if the model itself isn't publicly available. Using custom and proprietary architectures and datasets can reduce the risk of a white box attack (although not a black box attack). Limiting inference calls and transparency of the model (e.g. probability values) can help reduce the risk of both types of adversarial attacks, as well as risk of model theft.

**Data Transformations and Noise**: Often, naive attacks are fragile to transformations. Simply applying minor transformations will defeat adversaries, although it's highly dependent on how robust the attack is, models used, and intensity and type of the transformation. 
<figure>
  <img src="{{ 'assets/img/attackingdnns/cheeseburger-adversary-resnet18.png' | relative_url }}" width="600" height="500" class="center">
  <p style='text-align: center'>Examples of transformations on adversary, see <a href="https://github.com/aspfohl/hackingdnns/blob/main/hackingdnns/attacks/adversary.py">my script</a></p>
</figure> 
Even if the adversary is defeated, adversarial examples will often decrease the model's confidence and overall performance.

**Improving model's robustness**: [Ilyas et al. Adversarial Examples Are Not Bugs, They Are Features. 2019](https://arxiv.org/abs/1905.02175) argues adversarial examples can be directly attributed to the presence of non-robust features. By improving model's robustness and reducing overfitting through various techniques (e.g. regularization, dropout, ensembling, network distillation, etc.), models may be less susceptible to naive attacks.

**Adversarial training**: Adversarial training includes using adversarial examples during the training process so that the trained model will be more robust to future adversarial attacks. 

<!-- Libraries for generating and evaluating adversarial examples:
* [CleverHans](https://github.com/cleverhans-lab/cleverhans):  An adversarial example library for constructing attacks, building defenses, and benchmarking both
* [Adversarial Robustness Toolbox (ART)](https://github.com/Trusted-AI/adversarial-robustness-toolbox): Python Library for ML Security: Evasion, Poisoning, Extraction, Inference - Red and Blue Teams 
* [Foolbox](https://github.com/bethgelab/foolbox): A Python toolbox to create adversarial examples that fool neural networks in PyTorch, TensorFlow, and JAX
* [Adversarial-Attacks-PyTorch](https://github.com/Harry24k/adversarial-attacks-pytorch): PyTorch implementation of adversarial attacks

Publicly available adversarial examples:
* [NIPS 2017: Adversarial Learning Development Set](https://www.kaggle.com/datasets/google-brain/nips-2017-adversarial-learning-development-set)
* [APRICOT](https://apricot.mitre.org/): 1000+ images of objects with physical adversarial attacks
* [adversarial_qa](https://huggingface.co/datasets/adversarial_qa): 12,000 questions and answers with adversarial text
* [Natural Adversarial Examples](https://github.com/hendrycks/natural-adv-examples): real-world, unmodified, and naturally occurring examples that cause machine learning model performance to significantly degrade -->

While adversarial training can be a quick patch to known attacks, adding adversaries into training can significantly increase training time and is really only reactionary and not adaptive. Adversarial training may not account for novel attacks, although using open-sourced libraries that do regular patches could increase adaptivity and robustness.

Read more:
* [Is attacking machine learning easier than defending it?](http://www.cleverhans.io/security/privacy/ml/2017/02/15/why-attacking-machine-learning-is-easier-than-defending-it.html)
* [RobustML: List of open-sourced, white-box defenses for adversarial examples](https://www.robust-ml.org/defenses/)
* [Interpretable Machine Learning: Adversarial Examples](https://christophm.github.io/interpretable-ml-book/adversarial.html)
<!-- todo: link defending DNNs -->