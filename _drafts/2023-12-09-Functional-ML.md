# A Functional View on Modern Machine Learning

While the "neural" part of *(artificial) neural networks* has always been somewhat strenuous, in the days of the Rosenblatt's Perceptron and Fukushima's Neocognitron, at least the "network" part made sense. But already with the introduction of weight sharing in convolutional neural networks, the neat image of a network of artificial *neurons* begins to crumble. And looking at the architectures coming out of the current wave of artificial intelligence research, like the these days ubiquitous transformer, little of this original idea remains.

I'd like to argue here for a new way to view these things we tend to use in machine learning. 


## Parameter-Differential Function

While the internal structure of modern machine learning architectures like the transformer is rather complex, viewed in the abstract, they all have the following general form:

```hs
f :: [Tensor] -> [Tensor] -> Tensor
f params argument = _
```

with the (here omitted) body of `f` of a form such that `f` can be differentiated with respect to `params`. Such a function I will call here a *parameter-differentiable function* (pdf).

