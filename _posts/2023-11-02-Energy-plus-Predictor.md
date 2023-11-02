# We need both Energy and Direct Prediction

The world is unpredictable, at least in the sense that given the current state of the world, we can not exactly predict what the next state will be. Be that due to unobservable dynamics (e.g. the thought process of another actor influencing their actions), or simply due to the sheer complexity of the world exceeding what we can model with limited computational capacities. But the world also is predictable, in the sense that given the current state of the world, there are many states that are impossible, or rather highly *unlikely*, to come next, while some states are quite *likely*. Ergo our world model has to be probabilistic, in the sense that it does not directly predict a next state given the current state, but rather produces a probability distribution over the space of next states.

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


The prime example of such a world is language. Be it at the level of characters, words, or more generally some sort of token, there is a sufficiently low limit on the total number possible states (i.e. next tokens), such that we can directly represent, usually unnormalized, distributions over these tokens as values from a vector space, with each dimension corresponding to one world state. This is exactly what LLMs like GPT4 do, and with great success.

## Large or Infinite State Space

The problem is that the space of states of the real world, and by extension the space of any less abstract modal transformations of it, like vision or audition, isn't finite or even discrete. Or if we do consider it as finite, like all possible 8bit RGB images of a fixed width and height, the number of possible states is far too large to reasonable express distributions over the state space explicitly. What do we do?

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

I.e. our predictive world model no longer returns a directly representable value from a finite vector space, e.g. $distr \in \mathbb{R}^n$, but rather a a value from an infinite dimensional vector space, a function $distr \in \mathcal{S} \rightarrow \mathbb{R}$, e.g. $distr \in \mathbb{R}^m \rightarrow \mathbb{R}$.

But, as anyone familiar with the concept of currying from functional programming will know, there is a direct correspondence between the space of functions $X \rightarrow (Y \rightarrow Z)$ and the space of functions $(X \times Y) \rightarrow Z$. Therefore, instead of considering our predictive world model as a function $predict \in \mathcal{S} \rightarrow (\mathcal{S} \rightarrow \mathbb{R})$, we represent it as a function $predict \in (\mathcal{S} \times \mathcal{S}) \rightarrow \mathbb{R}$. We once again have a function that can be directly represented. All problems solved?

No, a very important practical problem remains: How do we sample from the distribution $distr \in \mathcal{S} \rightarrow \mathbb{R}$ that our predictive world model produces?

---

**To be elaborated:** We should learn both $predict \in \mathcal{S} \times \mathcal{S} \rightarrow \mathbb{R}$ and $expect(s') := \mathbb{E}_{s \in \mathcal{S}}(p_{predict}(s | s'))$. 