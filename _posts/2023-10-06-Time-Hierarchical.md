# Time Hierarchical Predictive World Model Architecture

This is an idea for an architecture for artificial intelligence.

Like quite a few people in the world of cognitive science and AI research, I consider the core of human intelligence a predictive model of the world, and the most important challenge to develop true AI to be: How can we build machines that also do this?


## Human Thought is Hierarchical

We can think about (i.e. reduce our active world model to) one grain of rice in a bowl, or about the bowl of rice among the other objects on the table, or about the table as one piece of furniture in the context of the whole room, and so on. But it is not the case that we really hold this whole hierarchy, this tree, in our head at once. When I'm considering the single grain of rice, the position of the table the bowl is standing on in which the rice grain is, is not something I consider at all. However, we can quickly and easily go up and down this hierarchy. After pondering the single grain of rice among its friends for a while, I can go up to the scale of the table in the room without having to re-orient myself.

Our thought isn't only spatially hierarchical, it also is hierarchical in two further dimensions: time and abstraction. We can think about what will happen in the next second, what will happen in the next minute, in the next hour, and so on. I predict how say a basketball will fly after a player threw it, anticipating whether it will hit the basket. But on a longer time scale, I also make predictions how many more points will be scored in the remainder of the game. And on an even longer time scale, I predict how the team will do in the season.

The abstraction hierarchy differs from the other two dimensions of space and time in that it doesn't correspond to anything physical in the world. I can consider the movement of another person's hand concretely, how the fingers curl, how the hand moves around, say if I were to want to dodge it in a boxing match. But I can also consider the movement of the hand more abstractly, as a particular boxing move, or a communicative gesture.

So the model of the world that we actively hold in our head at any point in time can be small scale or large scale (spatial), short term or long term (temporal), and concrete or abstract (abstraction). There are many levels in each of these three dimensions, and notably, these levels are not continuous. Rather, if we want to consider the world at say a different spatial scale, we make a discontinuous transition. E.g. from considering the grain of rice to considering the bowl as a whole.

But these three hierarchy dimensions are *not* uncorrelated! Transitioning a level up in one dimensions generally also corresponds to transitioning a level up in the other dimensions. E.g. When I go from thinking about the movement of an animals limbs about the animal as a whole, I also go from thinking about what will happen in the next fraction of a second to what will happen in the next few seconds, and I also go from thinking about the concrete dynamics of the animal's body, to the more abstract pathing and goals the animal is going for. The larger of a part of the world we are considering, the less concrete our representation can be, and the less short term our predictions can be. And the other way around, if want to think about the long term, the dynamics of a small part of the world become chaotic and untractable, so we have to consider the world at a larger scale and more abstractly.


## Hierarchical Planning

A big problem in engineering actually useful AI systems is: How the hell do you actually get it to make something happen? It's all well and good to have a model that passively observes the world, perhaps commenting on it in terms of image classifications or segmentations, or even by producing text. But in the end, we want a system that can strive for a long term goal, be it making me a cup of tea or helping humanity prosper.

So in the context of the hierarch(ies/y) of space, time and abstraction, what constitutes planning? What is a goal?

A goal is a high-level imposition on the world model. E.g. I consider a potted plant on the time scale of days or weeks and my world model predicts that it will whither. However, I do not want this to happen, so I impose onto the world model that the plant should still be healthy in a week. What does this mean? It's time for planning.

From a high-level imposition on the world model, a medium-level imposition is derived. I go from the time scale of the plant over the next week, to the time scale of the next few minutes. On this level, the imposition is that water should be added to the plant. From this I derive once again impositions for a lower level, at the scale of seconds, I should (1) go to the sink. Given my prediction about the world model what happens when I go to the sink together with the goal one level up of watering the plant, I generate an imposition for the next step, (2) get a cup. Then (3) put water into the cup. Then (4) go back. Then (5) water the plant. At each individual step, I go one level lower, imposing movements that my limbs should do to achieve the required actions.


## In Silico

There a many ways to try to distill this observation about hierarchical thought into an actually trainable and executable machine learning model. Here I will outline one sample from this distribution.

The input data to our model is a sequence of video frames. Each frame is embedded by an ordinary say convolutional neural network (which could simply be pre-trained off the shelf). The rest of the network does not interact with the actual video frames (i.e. we aren't doing stuff like auto-encoding), but only with their embeddings.

For each embedded video frame, we have a predictive model which predicts the embedding of the next frame. This models the short term dynamics. Call it the level 0 dynamics model.

From the level 0 embeddings, we derive level 1 embeddings by combining the level 0 embeddings of multiple (for simplicity, let's say two) time steps. This combination could either be a fixed function, like an average, or a learned function (questions arise here about how the training works). For these level 1 embeddings we also have a dynamics model, which predicts the next level 1 embedding from the current level 1 embedding.

And so on. We end up with a hierarchy of embeddings, with a dynamics model at each level that predicts increasingly long-term and abstract developments in the world.

Planning in this architecture happens by going from the top down. A goal translates to a change that is imposed on say the level 4 embedding. The dynamics across the next 16 time steps should differ from what is predicted. E.g. The prediction is that my favourite cup drops onto the floor and shatters. The imposition is that what actually happens should be that the cup is safely caught by the robot.

From this change in the level 4 embedding, changes to the predicted level 3 embeddings are imposed, constituting a vague plan what to do, which in turn result in changes imposed on the level 2 embeddings, and so on. In the end, we have changes that are imposed on the level 0 embeddings, which constitute the actual physical dynamics of what should happen, i.e. the way the robot should actuate it's arms to catch the cup.


## Training

At least if we use a fixed function for deriving higher-level embeddings from the embeddings below it, and a pre-trained video frame embedding function, training is straightforward.

From a given sequence of input data, we compute the level 0 embeddings, which then the level 0 dynamics model learns to predict. Given we can just directly compute the target higher level embeddings, we can just as easily train the higher level dynamics models.

For the top-down planning models, we randomly perturb the top-level embedding. From this a pertubation to the left embedding (assuming that our time scales are powers of two; For higher powers the following is repeated multiple times) one level down is predicted. Given the perturbed left embedding, the right embedding is predicted by the dynamics model. Given this new right embedding and the embedding one level up, a perturbation to the right embedding is predicted (It might be for example that the overall goal doesn't actually require any further action after some point, e.g. If I want a ball to be downhill, I can just give it a push and the rest is just the dynamics of the world without requiring any further intervention. In this case the planning model would not impose any further pertubation after the first one).

From this perturbed and predicted lower level sequence (or pair, in the case of power of two time scales), we can once again compute with the fixed bottom up function the resulting embedding one level up, which should be the same as the embedding we started with (Is there a danger of collapse here?). And so be backpropagate to update the planning model.


## Open Questions

Some of the above observations about the hierarchies in human thought are not incorporated into this architecture. For one, this architecture does always think at all levels of the hierarchy at once. Or at least it does not have any mechanism for the sort of transitions between levels that we make as humans. This could be considered a disadvantage given the fact that we humans constantly re-use patterns that we recognized at on level at another level. The architecture described here would have to learn these patterns at every level separately.

However, a thing to note is that the space and abstraction hierarchy dimensions are not directly constrained by the architecture in any way. Only how long of a time span is represented at each level of the model is fixed. There, at least in principle, it would be possible for the model to learn to use one level in the architecture for different spatial or abstraction levels, depending on the circumstance.

## Further Ideas

- Attention operating on the raw input data could be introduced, just like our visual attention mechanism. Instead of the level 0 embeddings representing the whole image, it might only represent a certain part of the image, or represent the image under some more general transformation. How is this attention steered?
