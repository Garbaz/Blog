# The Passive Actor: Planning as Hierarchical Prediction

This is an elaboration on parts of the [previous blog post](2023-10-06-Time-Hierarchical.md).


## Action as Prediction-Correction

The core idea is the following: Instead of considering the body of a cognitive agent and it's possible actions in the world as intrinsically separate from the rest of the world, consider it as a part of the world like any other, to be predictively modelled. Say for example "you" lift your arm as someone approaches. Just as the other person would predict that you will likely next wave your hand, so would you yourself. The core difference between prediction of the movements of another person and your own is that, while in the former case the only way to react to a violation of expectations is to adapt your world model (in the example, instead of one to wave, you might be one to greet people with a ✌️, which is something the other person can learn to expect), in the latter case, instead of adapting your expectations, you can adapt the movements of your limbs to be in line with the prediction (so in the example, as you predict that you will wave your hand, your muscles are activated such that your body is actuated in accordance).

That's the whole idea. Instead of a seperate world model predicting what will *happen* next, and an action module dictating what you *do* next, a unified model of the world and the self makes predictions. If the predictions about the own body do not align with the actual movements of the body, instead of necessarily adapting the world model, the body is instead compelled to comply with these predictions.

This explains how very low-level instinctive habbitual sequences of movements and such work, like the sequence to unscrew a water bottle and take a drink. However, of course, many of our actions do not occur as a direct consequence of preceding actions, but rather are informed by higher level "planning". How does this fit with this idea?


## Planning as Part of a Hierarchical Predictive World Model

Developing an architecture that can learn to do hierarchical planning of actions is in fact the core motivation for reconsidering action as part of a unified world model. That is, if this world model is hierarchical, i.e. if this world model not only makes predictions about the dynamics of the world over say the next second, but also, over multiple levels, predictions for longer time scales (and so generally also more abstract and/or for a bigger part of the world), then planning is simply a matter of top-down agreement of these time scales. See the [previous blog post](2023-10-06-Time-Hierarchical.md) for more about this.


(incomplete)