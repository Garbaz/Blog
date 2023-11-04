# We need both Prediction and Expectation

The world is unpredictable, at least in the sense that given the current state of the world, we can not exactly predict what the next state will be. Be that due to unobservable dynamics (e.g. the thought process of another actor influencing their actions), or simply due to the sheer complexity of the world exceeding what we can model with limited computational capacities. But the world is at the same time also predictable, in the sense that given the current state of the world, there are many states that are impossible, or rather highly *unlikely*, to come next, while some states are quite *likely*. Ergo our world model has to be probabilistic, in the sense that it does not directly predict a next state given the current state, but rather produces a probability distribution over the space of possible next states.


## Small Finite State Space

If the size of the space of possible world states is finite and sufficiently small, then this isn't much of a problem. We can directly represent the space of (unnormalized) distributions over the space of world states as a finite dimensional vector space.

In mathematical terms:

$$
\begin{align*}
\mathcal{S} & := \{state_1, ..., state_n\}\\
state & \in \mathcal{S}\\
distr & \in \mathbb{R}^n\\
p_{distr}(state_i) & \sim distr[i]
\end{align*}
$$

So a function predicting the next state from a previous state would be of the form:

$$
\begin{align*}
predict & \in \mathcal{S} \rightarrow \mathbb{R}^n\\
p_{predict}(state_i | state_j) & \sim predict(state_j)[i]
\end{align*}
$$


The prime example of such a world is language. Be it at the level of letters, words, or more generally some sort of token, there is a sufficiently low limit on the total number of possible next states, such that we can directly represent, usually unnormalized, distributions over these tokens as values from a vector space, with each dimension corresponding to one world state. This is exactly what LLMs like GPT4 do, and with great success.

## Large or Infinite State Space

The problem is that the space of states of the real world, and by extension the space of any less abstract modal transformations of it, like vision or audition, isn't finite or even discrete. Or if we do consider it as finite, like RGB images of a fixed width and height, the number of possible states is far too large to reasonable express distributions over the state space explicitly. What do we do?

Instead of some vector space of finite dimension, like $\mathbb{R}^n$ aka $\{1,..,n\} \rightarrow \mathbb{R}$, to represent distributions over possible states, we need an infinite dimensional vector space, like $\mathbb{R} \rightarrow \mathbb{R}$ or perhaps $\mathbb{R}^m \rightarrow \mathbb{R}$ for some $m \in \mathbb{N}$. Or really any space of functions $\mathcal{S} \rightarrow \mathbb{R}$ for some infinite space $\mathcal{S}$ representing the possible states of the world.

$$
\begin{align*}
state & \in \mathcal{S}\\
distr & \in \mathcal{S} \rightarrow \mathbb{R}\\
p_{distr}(state) & \sim distr(s)
\end{align*}
$$

So the predictive world model for a world with a high or infinite number of states is of the form:

$$
\begin{align*}
predict & \in \mathcal{S} \rightarrow (\mathcal{S} \rightarrow \mathbb{R})\\
p_{predict}(state | state') & \sim predict(s')(s)
\end{align*}
$$

I.e. our predictive world model no longer returns a directly representable value from a finite vector space, e.g. $distr \in \mathbb{R}^n$, but rather a a value from an infinite dimensional vector space, e.g. a function $distr \in \mathbb{R}^m \rightarrow \mathbb{R}$.

But, as anyone familiar with the concept of currying from functional programming will know, there is a direct correspondence between the space of functions $X \rightarrow (Y \rightarrow Z)$ and the space of functions $(X \times Y) \rightarrow Z$. Therefore, instead of considering our predictive world model as a function $predict \in \mathcal{S} \rightarrow (\mathcal{S} \rightarrow \mathbb{R})$, we represent it as a function $predict \in (\mathcal{S} \times \mathcal{S}) \rightarrow \mathbb{R}$. We once again have a function that can be directly represented. Problem solved?

Not quite, a rather big practical problem remains: How do we sample from the (implicit) distribution $distr \in \mathcal{S} \rightarrow \mathbb{R}$ that our predictive world model produces?


## Prediction Sampling

There is a very simple solution available to us. Instead of going through the entire trouble of representing somehow the full distribution of possible next states and then taking samples from it, we could just model the expectation of the distribution $\mathbb{E}_{predict}(s | s')$. In essence, we sidestep the whole issue of our prediction being by necessity probabilistic and pretend that it is deterministic. What's the issue?

The issue is that the expected value of a distribution is not necessarily a value with non-zero or significant probability weight.

Say we have a series of video frames showing a hand reaching for a face down playing card. Just as it is about to be turned over, our expected value based system has to make a prediction as to what will be in the next video frame. Since there is no information available as to what card will be on the other side, we will end up with all pixels in the predicted frame that are part of the front face of the card being the average of what it could be. I.e. we get a blurry mess.

This problem can be observed in real systems, and even with much more deterministic scenes. There are simply too many possible different continuations in video frames of real world scenes that a system modelling the expected value will give us actually possible continuations of the scene at hand.

So, we have not choice but to model the actual distribution and sample from it, right?

The good news is that this problem where we have some distribution $\pi$ for which we can compute $p_{\pi}(\cdot)$, but do not have any way to directly produce representative samples, is quite common. Consequently, much thought has been invested over the past decades into coming up with approaches to solve it. Today, there is a wealth of off-the-shelf algorithms that we could choose from. The bad news is that, every such algorithm comes with many prerequisites and caveats. There is no silver bullet when it comes to sampling procedures.

And these caveats are not just theoretical imperfections. In the end, in one way or another, these algorithms have no choice but to somehow pull values out of some (oh so cleverly crafted) hat at will and check how probable these values are under the given distribution $\pi$. The wonkier the shape of the probability landscape under $\pi$, the difficulter it becomes to effectively do this pulling out the hat procedure. As a consequence, it generally requires many sampling steps to actually get values representative of our distribution.

So, here's what I would like to argue in this blog post:

We need to learn both a function $predict \in \mathcal{S} \times \mathcal{S} \rightarrow \mathbb{R}$ modelling the actual distribution of possible continuations, and a function $expect(s') \approx \mathbb{E}_{predict}(s | s')$ modelling the expectation of possible continuations.

Why?

## $predict$ and $expect$

To go back to the example scenario given earlier with the playing card about to be revealed. Our system modelling the expected value of the next video frame produces a blurry mess of a playing card, which of course is not actually a possible next value. The thing is though, this blurry mess of a prediction is not any old blurry mess. It is a blurry mess that is quite close to the actually possible continuations, sitting in a valley of low probability (or hill of high energy, same same) in between high probability values.

As I said earlier, in general, sampling procedures have a hard time with rocky probability terrain, and require significant time to converge to good samples. But the thing is, a big factor for many of these procedures in determining how fast it will converge is the choice of starting point.

So we have a function $predict$ which gives us a distribution we want to sample from, and a function $expect$ which gives us a value that is, ideally, close to some high-probability values, even if itself is of low-probability. Ergo:

*¿Por Qué No Los Dos?*

Compute the expected value with $expect$, and take it as a starting point to sample from the distribution given by $predict$.



---
---

*To be elaborated:* 

- Why not just the current state? => Continuous and discontinuous transitions in world state.

- (How) do we represent uncertainty about the current state?

- Multiple $expect$ functions (like multiple attention heads; ?) || A noise-parametrised $expect$ function to quickly re-sample a new (reasonable) starting point (?) || Or can we just have noise in the sampling prodecure (?)