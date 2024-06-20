# Company specific

-------------------------------------------------


## Technical Problems

**Testing ML models**

# Testing

Unit tests: tests on individual components that each have a single responsibility (ex. function that filters a list).

Integration tests: tests on the combined functionality of individual components (ex. data processing).

Regression tests: tests based on errors we've seen before to ensure new changes don't reintroduce them.

ML systems can run to completion without throwing any exceptions / errors but can produce incorrect systems
`Unit and integration testing of all the preprocessing code` :

Testing framework - Pytest

```
# tests/food/test_fruits.py
def test_is_crisp():
    assert is_crisp(fruit="apple")
    assert is_crisp(fruit="Apple")
    assert not is_crisp(fruit="orange")
    with pytest.raises(ValueError):
        is_crisp(fruit=None)
        is_crisp(fruit="pear")
```
Can parametrize to avoid redundancy

```
@pytest.mark.parametrize(
    "fruit, crisp",
    [
        ("apple", True),
        ("Apple", True),
        ("orange", False),
    ],
)
def test_is_crisp_parametrize(fruit, crisp):
    assert is_crisp(fruit=fruit) == crisp

```


`Testing the Data`

We used to get data from the client and someone else is responsible for cleaning and uploading it to S3. Now the thing in machine learning is it fails silently.

Test the column types,uniqueness, distribution ranges, missingness etc. Tool: `GreatExpectations`
```
# Unique values
df.expect_column_values_to_be_unique(column="id")
```

`Testing Models`

Some basic tests on models like

- dec loss after one batch
- overfit on a batch

  `Behavioral testing`

  Imagine you've updated a sentiment analysis system you've trained and tested on you. Now in the same sentence you replace with synonyms, output label shouldn't change `invariance testing`. Now you might have `directional expectations`. For us we introduced noise and checked action consistency.

**Improving latency**

One technical problem you solved:
use profilers: line_profiler,memory_profiler etc (eg: $ python -m cProfile -o profile.stats julia1.py)
step 1: algorithmic inefficiencies
step 2: using multiple libraries. are you using them well ?
python level optimizations: use generators or lazy evaluation, use tuples for static arrays and lists for dynamic(more memory is initialized)
eg: find how many of the first 100 Fibonacci numbers are div by 3
tuple is cached, not garbage collected immediately.=> creating tuple is 5 times faster than a list.

numpy: don't create copies, use views.
---big effects---
for memory: we often don't need float64 for all features. if we can use say float16=> 64gb ram to 16gb ram
since garbage collected better to del after the use of large variables
reduce network size, distillation.
Last resort => multiprocessing module. (creating indicators, used joblib to create them in parallel)
cpu-bound (only multiprocessing can help)





## Niveshi

**Summary of work done at niveshi**

Permutation testing, add weights that multiply each feature in first layer(init = 1) and + put l1 norm + variance loss(force weights to be varied), progressively remove those that are close to zero and retrain.

Problems Solved

Started from a scratch pad. No code.

Ambiguity: we have architecture, loss-functions, environment(what is an environment),typically in RL agent is trained for multiple episodes. In games definition of episode is self-evident. How do you methodically go about a working model.

- Lot of moving parts. We need to fix somethings,defining goals itself is very difficult.
Here's the plan:

**1. Training consistency.**

Goal: action consistency.

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

## Energylab internship.

Incorporated Skyimages for the forecasting of solar-irradiance. Short-horizon = 10.

How ?

Two-stage:
Built a custom CNN+LSTM model (late fusion) that take both the past few skyimages(cloud cover) and sensor data.

Noted a peculiar problem: Temporal bias=> predictions are just shifted versions of past. Over short horizon this gives less MSE and model doesn't have to learn any patterns.

**Contribution**

Quantify and reduce temporal bias. How to quantify => created a metric. Take predicted and true signals. now create a new signal = by adding noise on the true signal.

now calculate spearman rank correlation btw (noise,true)=> `reflects the value when there is no temporal bias` and pred,true.

Fitted an SVR on the residuals with some custom features via feature engineering.(note all this is done on validation data as we already used train-data).

-------------------------------------




## Recommender Systems

Modeling:
Memory based methods.(no weights are learned)

Similarity based on User-item matrix.

similarity => jaccard(intersection over union),cosine(takes scale into account), pearson-similarity(normalize,some users give high ratings naturally)

User-user or item-item. => to find a recommendation for a user, find similar users and then take their ratings for this item and calculate the score for each unseen item and return the one with the highest score.

item-item pros: 

- Being item-centric, it overcomes
the issue with sparsity in user data.

- It also
overcomes challenges with newer and less-frequent users with
sparse history, because the similar items list focuses on the
user’s history as opposed to the history of other users


NCR: neural collaborative reasoning

1. Recsys can be seen as reasoning task, if user likes a,b,c=> does it imply he'll like d.(propositional logic)

<img width="582" alt="Screen Shot 2023-07-09 at 4 09 39 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/2fb5ed1b-0305-43fb-82fe-6991936be9a2">

2. Use neural nets to encode logic gates. Like `Not`.

Regularize not(not(x) = x, thus similarity (consine) should be low. similarly for commutative and associative properties.

3. Loss => constrastive loss with negative sampling. We have fixed vectors `T` and `F` and for each positive, we take another negative and force them to be as far away as possible.

<img width="611" alt="Screen Shot 2023-07-09 at 4 14 43 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/e985f081-6105-4e62-96ef-e477add0a7f1">

4. Detail: max training history is 5. Metrics (NDCG@N, Hit@N, precision@N and recall@N),ndcg takes position into account and works even if we have relevance scores. Here we just took rating values and thresholded into positive and negative classes.
   
   <img width="400" alt="Screen Shot 2023-07-09 at 3 47 26 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/2cb49cf8-584e-4c57-8af0-163a00e38419">

6. StrongBase line : NMF. = user bias + item bias + (user.item), embeddings are learned.

   **Big contribution**: Counter factual analysis, can tell the user why we recommended the item.

   How ? negate each interaction(positive to negative) and see the effect (can be done at test time) on say hit@5. The one with highest impact is most important, thus can tell the user => we recommended this movie because you've liked this in the past. 

----------------------------------------------


## Project 1: Wave2vec2.0

- ASR model trained by self-supervision. (they used quantization with some details to identify speech vocabulary). Loss is contrastive loss => minimize (softmax) over 1 true label and `k` sampled possibilities. Then they finetuned by putting a linear layer that outputs vocabulary.

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
- use any image and text encoder. Now we have a constrastive objective.(softmax along the row and column)

The loss function is for each row - normalize the values and use cross-entropy loss, and now do the same for that column, and avg it.

```
# image_encoder - ResNet or Vision Transformer
# text_encoder - CBOW or Text Transformer
# I[n, h, w, c] - minibatch of aligned images
# T[n, l] - minibatch of aligned texts
# W_i[d_i, d_e] - learned proj of image to embed
# W_t[d_t, d_e] - learned proj of text to embed
# t - learned temperature parameter
# extract feature representations of each modality
I_f = image_encoder(I) #[n, d_i]
T_f = text_encoder(T) #[n, d_t]
# joint multimodal embedding [n, d_e]
I_e = l2_normalize(np.dot(I_f, W_i), axis=1)
T_e = l2_normalize(np.dot(T_f, W_t), axis=1)
# scaled pairwise cosine similarities [n, n]
logits = np.dot(I_e, T_e.T) * np.exp(t)
# symmetric loss function
labels = np.arange(n)
loss_i = cross_entropy_loss(logits, labels, axis=0)
loss_t = cross_entropy_loss(logits, labels, axis=1)
loss = (loss_i + loss_t)/2
Figure 3. Numpy-like pseudocode for the core of an implementation of CLIP.
```
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

ASK:

1. what's the team-size you are aiming now
2. What's your vision








