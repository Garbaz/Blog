# Energy Function as Agreement Coordinator between Interpretations

In an Energy Based Model, instead of learning some function `f : X -> Y` that tells you "given `x` what value should `y` be?", you learn a function `e : X, Y -> [0,∞)` which tells you "given this `x` and this `y`, how well do they fit together?", a low value of `e(x,y)`, a low energy, meaning that they do fit, and vice versa. Then, if we do want to a value `y` from some given value `x`, we use gradient descend *during runtime* on `e` to go from some initial guess at `y` to a more and more suitable `y` for the given `x`.

This approach can be used directly instead of normal deep learning, with a bunch of advantages, in particular in how `e` can be learned. However, what I'm interested in considering here is an energy based model not simply as a method for replacing a traditional `X->Y` function, but rather as a method to coordinate different predictive world models.


## Coordination between Modalities

Say you open your eyes and in front of you you see a heavy steel ball right above a wine glass, and it has a noticable downward velocity. What do you expect? You expect it to hit the wine glass of course. That's your visual prediction. You are not surprised when the wine glass shatters to pieces, and you are not surprised at the noise of shattering glass that reaches your ears. But on the other hand say you had not opened your eyes, instead keenly listening to your surroundings. The steel ball flying through the air does not make any noise, nor does the wine glass standing still. Then suddenly, an explosion of noise of shattering glass. Are you surprised? Yes. Given the previous silence, you did not expect this noise.

Predictions, and therefore expectations, in different modalities are coordinated. Given what you predict visually influences what you predicted auditorily. And if instead of the wine glass to be hit by the steel ball, it was your fingers that you visually saw just about to be hit by the steel ball, you would predict pain in your fingers, even though before the ball hits, your fingers feel nothing ususual.

But say you have earphones on, listening to an audio book, while walking through the streets. Your auditory model predicts what you will hear next (voices and noises from your headphones), while your visual model predicts what you will see next. These predictions are not related, since the contents of the audio book have nothing to do with the reality around you. So even though say your visual model predicts the next movement of the car on the road ahead, and your auditory model makes no prediction about how that would sound, instead predicting the next word said by the narrator, no agreement is enforced. 

Coordination is not the same as having one coherent model across all modalities. It is about ensuring that different predictive models do not make predictions that disagree. If what I am seeing is not what I can hear, there is no disagreement.


## Beliefs as Virtual Modalities

Say I have a closed wooden box in my hand. If I were to open it, what do I expect?

Say I believe that the closed shoe box in my hand contains a stack of cash, because someone told me so. Now I open the box. I am surprised to see no ball in the box. But why? A predictive model based on the visual information has no reason to predict to see a tennis ball in the shoe box.

Just as different modalities make possibly, but not necessarily, overlapping predictions about the world, beliefs also make predictions that have to agree with other beliefs and with modalities. Coordination between modalities, between modality and belief, and between beliefs is all the same, even though modalities derive their state and prediction from outside information, while beliefs are intrinsic.


## Energy as Agreement

Take an energy based "surprisal" model (see [today's other blog post](2023-10-27-Energy-Surprisal.md)) for one modality, say one that judges given the embedding of a current frame of video and the embedding of a next frame of video, whether this next frame would be surprising given the current frame, and another modality, say one that judges given the embedding of a current second of audio and the embedding of a next second of audio whether this next second of audio would be surprising given the current second of audio.

The embedding spaces of these two modalities are completely unrelated, encoding different information and the same information in different ways. However, that is not to say that the energy functions are completely unrelated. Generally, when what is heard is highly predictable, what is seen is also highly predictable. I.e. given a sample of a current frame with it's audio, and a sample of a next frame with it's audio, generally, you do expect that if the visual model is surprised, then the auditory model is also surprised.

Just like the surprisal models, we introduce an energy based "agreement" model. This model takes a given visual embedding and an auditory embedding and judges whether there is a disagreement about the state of the world.

---

**WIP WIP WIP**

But how do we train this?

Use agreement in surprisal (as hinted above)? Or agreement in certainty (i.e. how concentrated are the different energy functions)?

Also: In a real analog systems, we don't have the "current frame" and the "next frame" as memory locations. Rather our surprisal function would be the current state of some embedding function's output and it's derivative (?).