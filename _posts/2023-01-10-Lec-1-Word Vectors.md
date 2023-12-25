---
title: CS224N Lecture 1. Word Vectors
categories: [Stanford, CS224N Winter 2023]
tags: [AI, NLP]
author: <na_id>
math: true
img_path: /assets/img/cs224n/1
---

## 1 Introduction to Natural Language Processing
Natural language processing is a field of science and engineering focused on the development and study of **<u>automatic systems that understand and generate natural (i.e, human) languages</u>**.

### 1.1 Humans and language
Human languages are communicative devices enabling the efficient sharing and storage of complex ideas, facts, and intents. As [Manning, 2022] argues, the complexity of communication enabled by language is a uniquely human intelligence among species.

### 1.2 Languages and machines

![cartoon](cartoon.png)

> **Language isn't a formal system, language is glorious chaos.** You can never know for sure what any words will mean to anyone, all you can do is try to get better at guessing how your words affect people. So you can have a chance of finding the ones that will make them feel something like what you want them to feel. Everything else is pointless.

So that's ultimately what our goal is, how to do a better job at building computational systems
that try to get better at guessing how their words will affect other people and what other people are meaning by the words that they choose to say.

## 2 Representing words
### 2.1 Signifier and signified
The commonest way that linguists think about meaning: a pairing between a word which is a signifier or symbol, and the thing that it signifies, the signified thing which is an idea or thing.

![word meaning](signify.png){: w="350" h="200" }


### 2.2 Representing words as discrete symbols

### 2.3 Representing words by their context
> You shall know a word by the company it keeps.
—— J. R Firth, British linguist

**Distributional semantics**: A word’s meaning is given by the words that frequently appear close-by

When a word $w$ appears in a text, its context is the set of words that appear nearby (within a fixed-size window). We use the many contexts of $w$ to build up a representation of $w$.

![context](context.png){: w="500" h="300" }


## 3 Word2vec

### 3.1 Overview

Word2vec (Mikolov et al. 2013) is a framework for learning word vectors.


![example](example.png){: w="500" h="200" }

> **Idea**: We have a large corpus ("body") of text: a long list of words. Every word in a fixed vocabulary is represented by a vector. Go through each position $t$ in the text, which has a center word $c$ and context ("outside") words $o$. Use the similarity of the word vectors for $c$ and $o$ to calculate the probability of $o$ given $c$ (or vice versa). Keep adjusting the word vectors to maximize this probability.
{: .prompt-tip}

### 3.2 Objective function
The big question then is, what are we doing for working out the probability of a word occurring in the context of the center word?

We want to maximize the likelihood of the context we see around center words. But following standard practice, we slightly fiddle that because rather than dealing with products, it's easier to deal with sums.

![object function](obj_func.png){: w="600" h="500" }

![object function](obj_func2.png){: w="500" h="450" }

### 3.3 Prediction function

![prediction function](predict_func.png){: w="500" h="450" }

### 3.4 Working through a gradient

$\theta$ represents all the model parameters, in one long vector. In our case, with $d$-dimensional vectors and $V$-many words, we have

$$

\theta = \left[
\begin{matrix}
v_{aardvark} \\
v_a \\
. \\
. \\
. \\
v_{zebra} \\
u_{aardvark} \\
u_{a} \\
. \\
. \\
. \\
u_{zebra}
\end{matrix}
\right] \in \mathbb{R}^{2dV}
$$

We optimize these parameters by walking down the gradient. We compute all vector gradients!


$$
\frac{\partial}{\partial v_c} \log \frac{e^{u_o^{\mathsf{T}}v_c}}{\sum_{w=1}^{V}e^{u_w^{\mathsf{T}}v_c}} = \underbrace{\frac{\partial}{\partial v_c} \log e^{u_o^{\mathsf{T}}v_c}}_{Part A} - \underbrace{\frac{\partial}{\partial v_c} \log \sum_{w=1}^{V}e^{u_w^{\mathsf{T}}v_c}}_{Part B}
$$

For Part A,
$$
\frac{\partial}{\partial v_c} \log e^{u_o^{\mathsf{T}}v_c} = \frac{\partial}{\partial v_c} {u_o^{\mathsf{T}}v_c} = u_o
$$

For Part B, based on chain rule:
$$
\frac{\partial}{\partial v_c} f(\overbrace{g(v_c)}^z) = \frac{\partial f}{\partial z} \cdot \frac{\partial z}{\partial v_c}
$$,

$$
\begin{eqnarray}
\frac{\partial}{\partial v_c} \underbrace{\log}_{f} \underbrace{\sum_{w=1}^{V}e^{u_w^{\mathsf{T}}v_c}}_{z=g(v_c)}
&=& \frac{1}{\sum_{w=1}^{V}e^{u_w^{\mathsf{T}}v_c}} \cdot \underbrace{\frac{\partial}{\partial v_c} \sum_{x=1}^{V}e^{u_x^{\mathsf{T}}v_c}}_{Part C} \\
\end{eqnarray}
$$

For Part C,

$$
\begin{eqnarray}
\frac{\partial}{\partial v_c} \sum_{x=1}^{V}e^{u_x^{\mathsf{T}}v_c}
&=& \sum_{x=1}^{V} \frac{\partial}{\partial v_c} {\underbrace{e}_{f}}^{\overbrace{u_x^{\mathsf{T}}v_c}^{z=g(v_c)}} \\
&=& \sum_{x=1}^{V} e^{u_x^{\mathsf{T}}v_c} \frac{\partial}{\partial v_c} u_x^{\mathsf{T}}v_c \\
&=& \sum_{x=1}^{V} e^{u_x^{\mathsf{T}}v_c}  u_x
\end{eqnarray}
$$

Hence,

$$
\begin{eqnarray}
\frac{\partial}{\partial v_c} \log(p(o|c))
&=& u_o - \frac{1}{\sum_{w=1}^{V}e^{u_w^{\mathsf{T}}v_c}}\left({\sum_{x=1}^{V} e^{u_x^{\mathsf{T}}v_c}  u_x} \right) \\
&=& u_o - \sum_{x=1}^{V} \frac{e^{u_x^{\mathsf{T}}v_c}}{\sum_{w=1}^{V}e^{u_w^{\mathsf{T}}v_c}} u_x \\
&=& \text{observed} - \text{expectation}
\end{eqnarray}
$$

> This is just the derivatives for the ceter vector parameters. Also need derivatives for output vector parameters. Then we have derivative w.r.t. all parameters and can minimize the objective function.
{: .prompt-warning}
