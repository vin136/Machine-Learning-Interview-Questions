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

## Project 1: Wave2vec2.0

- ASR model trained by self-supervision.

Knowledge distillation : KL between teacher and student. inspiration from distil bert(alternative initialization),reduce number of transformer layers and initialize alternatively. And force the last layer activations to be same(l2).

Quantization: dynamic quantization to quantize linear layers of wav2vec 2.0

- dynamic: weights are quantized to int8 and activations are tuned in real-time(the scaling factor). Overhead in terms of compute but need not know any info about data beforehand.
- static: everything is done beforehand.

`Big-idea`: store scale and shift in map float,rest all  to int.

There's also quantization aware training.

## Project 2: Fine-grained video search.

Goal: we have a search token and we should be able to index inside a video based on the content.

A video of a person doing a task say cooking a meal.
Now we have sequence of text-descriptiors...like cut the onion, heat the pan etc. Map each to the corresponding frames(start and end). Some frames are noise are useless.

Download,preprocess etc.

Get embeddigns of these frames.

Problems: Video length is not all same, and also text-sequence varies quite a lot.(sample the videos to have fixed length,let text seq vary.)

ML formulation:

- For each frame classify each of it to 0 to n(being the index of text sequence).

How do you go about solving this problem ?

Step 1: You need strong baselines

Think some:

- Baseline 1: Quantify feature quality.

If features are good similarity scores should be high across the original chunk. To quickly quantify if it's good, using the similarity scores alone i should be able to find the `st` and `en` frames(two).Simple linear model.

The loss, I made is a differentiable version of the metric IOU (GIOU).
If the features are any good it should be noticeably different from a random model.

- Baseline 2:  Use raw embeddings (sequence of vectors) + a text - vector and maximize the score
various modeling choices : LSTM + attention(text-embedding)

We are taking each annotation independently, can we jointly take two sequences and predict the output

- Attend and align: 

Thougts: Can also be treated as a seq to seq model ?

I want to maximize the joint => P(y1,y2,...| X)
just like language, typical solution is optimize on token level cross-entropy.

I want all my outputs in one shot, there's some inductive bias => monotonicity because text is ordered sequence during training.

Self attention between video frames + cross attention .

`CLIP Model`

We want a model that connects images and text really well. Data is easy to get,people put photos and give labels is social media.

- Predicting the text from photo in language modeling isn't a great idea
- use any image and text encoder. Now we have a constrastive objective.

Needs large enough minibatch.

<img width="500" alt="Screen Shot 2023-06-19 at 1 13 57 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/ea1eba3b-745f-45fc-97f0-07a3750a6051">

At inference ,zero shot

<img width="500" alt="Screen Shot 2023-06-19 at 1 15 44 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/e873a3ea-37c0-4ec0-bcfb-5cae0315bf79">

` Encoders`

Text-encoder: a transformer

Image-encoder: VIT, resnet etc

Zero-shot is as good as fully supervised resnet on imagenet.(crazy)


GPT -> DECODER ONLY
T5->Encoder,decoder
BERT -> ENCODER ONLY, masked language madeling.

- Birectional encoder(transformer encoder), mask out tokens, predict missing tokens.
- since at test there is no masking=> to replicate purturb for 10% times with random token instead of MASk, N LEAVE IT UNCHANGED FOR SOME %.
- sentence pair prediction scheme: [cls],[sep],[mask] tokens

When finetuning , finetune the embedding of [CLS] token









