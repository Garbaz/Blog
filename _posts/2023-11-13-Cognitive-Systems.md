# A Perspective on Cognitive Systems

A system for my purposes is simply a subset of some larger world. We define that the configuration of this subset is the *internal state* of the system, whereas the configuration of everything else is the *external state*. Similarly, we separate the overall dynamics into the *internal dynamics* and the *external dynamics*. However, there are parts of the world which we can neither assign to the internal state nor the external state, the configuration of all such parts together is the *coupling state*.

If we view for example the human brain from this system perspective, then the configuration of the brain, what we call the mind, is the internal state, the configuration of the outside world constitutes the external state. The coupling state is the state of the senses and the muscles of the body the brain is attached to, the *modalities*.

To note is that while it is purely a matter of definition and perspective what part of the world state we choose to be the internal state, once we have done so, what is the external state and what is the coupling state follows automatically.

In the terminology of probability theory, what I call here the "coupling state" is the [Markov Boundary](https://en.wikipedia.org/wiki/Markov_blanket), conditioned on with the internal state and the external state are independent.

We can divide the coupling state into three parts: The *sensory state*, the *action state* and the *possessive state*. The sensory state is in effect a part of the external state, in that it is only affected by the external dynamics and entirely independent from the internal dynamics, while however influencing the internal state (E.g. The activity of the photoreceptors in the eye). The action state is the dual to the sensory state, it is only affected by the internal state, but it's state influences the external state (E.g. The signals traveling to the nerves in your muscles). The possessive state is anything that is influenced both by the external and internal state (E.g. If we define our system to be the entire human body and not just the brain, the configuration of our limbs. While the internal dynamics of my nervous system cause them to move, so do external forces).

To put all this in mathematical terms, with external state $$e$$, internal state $$y$$, sensory state $$x$$, action state $$a$$, and possessive state $$p$$:

$$
\begin{align*}
\dot{e} & \sim F(e, a, p)\\
x & \sim R(e)\\
a & \sim A(y)\\
p & \sim Q(e, y)\\
\dot{y} & \sim D(y, x, p)\\
\end{align*}
$$

I will assume in the following a slight simplification. That being that our system is *ideal*, meaning that the coupling state consists only of the sensory and the action state, without any possessive state. For any sensibly defined system, such as the human mind or a computer, this is a reasonable approximation to make.

I will also for the most part consider a *passive system*, one for which the coupling state is purely sensory, i.e. it has no influence on the external state. Though I will get back to the specific topic of *active systems* later.


## Intelligent and Cognitive Systems

A *learning system* is one in which the internal dynamics appear to be such that it optimizes it's internal state under some measure of optimality. So in mathematical terms, with the optimality measure $$\sigma$$:

$$
D(y,x) = \mathop{argopt} (\sigma(y, x))
$$

There are two kinds of optimization behaviors that I would like to consider here: *intelligence* and *cognition*.

(Note that "intelligence" and "cognition" are used here purely as technical terms to describe different optimization behaviors. They ascribe no value to the system in the way we humans tend to. To that end, I also use the word "cognition" rather than "consciousness" here, since in my eyes, "consciousness" is a term so overloaded with meaning to be entirely useless)

Intelligence is an optimality measure that is purely *extrinsic*, i.e. instead of $$\sigma(y,x)$$, we only have $$\sigma(x)$$. An intelligent system appears to maximize some kind of *reward*. For example, one such optimization measure is the hormonal signaling that pushes the minds of animals such as us humans to scavenge and reproduce.

In the terminology of machine learning, what I call here "intelligence" is [Reinforcement Learning](https://en.wikipedia.org/wiki/Reinforcement_learning.).

Cognition on the other hand is a somewhat more complex optimization behavior. A cognitive system appears to minimize *surprisal*. Or put a different way, it seemingly tries to *predict* the sensory state $$x$$.

The internal state of a cognitive system can be split into two parts, the *prediction state* $$\hat{x}$$ and the *model state* $$m$$. Similarly, the dynamics of a cognitive system is decomposed to form the following relations, with $$\delta$$ some distance measure:

$$
\begin{align*}
\sigma((\hat{x}, m), x) & = \delta(\hat{x}, x)\\
\hat{x} & \sim E(m)\\
\dot{m} & \sim T(m)\\
\end{align*}
$$

If we once again adopt machine learning terminology for a moment and take a perspective on the world as a whole, the "sensory state" and "prediction state" are [Joint Embeddings](https://openreview.net/pdf?id=BZ5a1r-kVsf) of the external state and the model state respectively, and our optimization measure is consequently an energy function. The difference to a typical setup being that instead of the embedding space and the two embedding functions being what we learn, one of the embedding functions, the "projection" from the "external state" to the "sensory state", is given, and consequently the structure of the embedding space is also predefined. Rather, what we learn is only one embedding function, and the structure of the input space to it.

In the human mind, this cognitive optimization behavior manifests in the form of expectations that we have. If I flip a coin in the air, catch it and slap it on the table, the mind expects to see a coin again once the hand is lifted, and not nothing or a rose petal. We also expect it to be heads or tails, and not for it to suddenly be blank.


## Why Cognition?

In short: The model state internal to a cognitive system converges to mirror the external state of the world. Consequently, as it's name suggests, it becomes a model of the external world. How so?

Consider what it means to minimize the distance between the sensory state and prediction state. Ideally the prediction state will always be equal to the sensory state. However, the sensory state only depends on the external state, and the prediction state only depends on on the model state. And so in turn does the dynamics of the sensory state depend on the external dynamics, while the dynamics of the prediction state depends on the model dynamics. In mathematical terms:

$$
\begin{align*}
&             & \hat{x} & = x\\
& \Rightarrow & E(m) & = R(e)\\
\end{align*}

$$

and

$$
\begin{align*}
&             & \dot{\hat{x}} & = \dot{x}\\
& \Rightarrow & \dot{m} \nabla E(m) & = \dot{e} \nabla R(e)\\
& \Rightarrow & T(m) \nabla E(m) & = F(e) \nabla R(e)\\
& \Rightarrow & (T\nabla E)(m) & = (F\nabla R)(e)
\end{align*}
$$


## But *Why* Cognition?

Because cognition is highly useful for intelligence.

The internal dynamics of an intelligent system are defined by the optimization of a metric that is only influenced by the sensory state. Especially when this metric is very sparse over time, as is the case in many settings both natural and engineered, to be able to *anticipate* the future dynamics of the metric helps greatly in optimizing for it.

To take a quite different example to the human mind in the real world, consider the world of a chess game. The state space of chess is tiny compared to the state space of the world, and even discrete, and yet, even there, we already come upon this fundamental problem: The signal whether a move was good or bad only comes when the game is over. And yet, people and machines alike can choose good moves, they can *plan*. How is that possible? By combining cognition and intelligence.

**TO BE CONTINUED**


<!-- > I'm reconsidering the way I defined intelligence, the way I'm defining it below makes more sense. I should give what I defined as intelligent above maybe a different name (?)

An intelligent system appears to optimize some kind of *reward* function $\rho(x)$:

$$
D(y,x) = \mathop{argopt}\limits_y \left (\int_\text{now}^\infty \rho(x) \, dt \right )
$$

The problem is that this integral can not be evaluated. 

---

### Stuff to touch on

- (-> voice recorder)
- 
- AI safety (a purely cognitive (passive) system is by definition no threat; It's only when you introduce extrinsic reward when you get problems)
- Autism (or maybe better not, I don't like it when people speculate about autism; But anyway, the idea would be that autism is roughly speaking when the intrinsic surprisal metric outweighs extrinsic reward, or simply is higher than usual. Hence we are overwhelmed by noise & chaos (unpredictable), like order, like to *really* understand (either the world, like me, or some arbitrarily chosen subsection of the world, i.e. special interests)) -->
