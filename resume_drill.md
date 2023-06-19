## Niveshi

Problems Solved

Started from a scratch pad. No code.

Ambiguity: we have architecture, loss-functions, environment(what is an environment),typically in RL agent is trained for multiple episodes. In games definition of episode is self-evident. How do you methodically go about a working model.

- Lot of moving parts. We need to fix somethings,defining goals itself is very difficult.
Here's the plan:

**1. Training consistency.**

Keep other parts simple: CNN based architecture + loss function: `A3C`

Define metrics of importance: trade_specific: avg.return,sharpe_ratio,loss_function_specific:policy loss,value loss etc.
Goal is to have control on training process(not even thinking about validation set yet)

`some interesting stuff`: during training your loss does't typically go down. it's not an optimization problem. Absolute value has no meaning.

Goal: the variation from epoch to epoch should be gradual in terms of global metrics.

`Ton of experimentation`: not go out read interesting papers n implement but u need to form a hypothesis, identify a problem and then lookout. This gives you confidence.

Solution: PPO (proximal policy optimization)


**2. Now overfitting => minimize train,valid skew.**

Goal: Identify levers that effect this, some are obvious, some less so.

network size,(architecture,depth),(weight decay, drop out, Batch norm etc)

identify if training is going stable: (activatios/gradient norm) is it in reasonable range. clip gradients.

`Some interesting stuff`: 

1. Batch norm doesn't work in RL. Why ?
2. Is the ordering of information important across the volume dimension. Input is a stack of squares but across depth info is not homonegous. and CNN is permutation invariant, just like attention. How to give the location information ?

`RL is noisy fundamentally`: credit assignment over a temporally long chain of actions.Imagine playing chess and u got a check-mate. BN is noisy too.

Be careful for information leakage. Don't look at test performance at all.

`Solution`: 

1. massively reduced the architechure size, made it as deep as possible(use residual connections) + efficient(used seperable convolutions)

2. Multi-objective training, can have regularization effect, intelligent ensembling



**3. Now valid and test skew => not as good.**

Either we overfit(by snooping and doing too many iterations) or Valid is not a good reflection of test.

`One idea`: train on the whole data, divide the data based on the profit. This is a gold reflection of the distribution according to the model.

Now split data onto train,valid,test ? is this good ?

------------------------------










