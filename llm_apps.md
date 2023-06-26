Ambiguity
 - in output format = downstream applications might rely on a specific format.
 LLM's can be used for few shot learning. give some examples and it's labels and ask it to generate label for the new examples
 
 Prompt optimization = chain of thought(ask it to explain itself),take ensemble average of multiple outputs.
 
 Input token length doesn't affect latency that much (in parallel) but output seq length impacts latency.
 
 finetuning benefits
 - can train on more examples
 - more instructions can be baked in thus less tokens during the prompt,less cost.

Generate GPT-3 Embeddings and then use .. recsys + vector databases.

`question1`
Backward and forward compatibility:
- When you get new data, you finetune/retrain. Now does your earlier prompts still work ? you got to think/test all your pipeline.

Compose a task = eg: talk to your data application.

`question2`
testing in case of compositional tasks.

One large commercial application of LLM: `Search and recommendation`

## How are you tracking prompts/engineering ?(maybe with git).

How to quantify improvement ? reasonable to expect the performance improves in one way and degrades in another.

What are your proxies for `overfitting`(train vs eval) and `domain-shift`(eval vs test-set) and drift ?

Building evaluation set for your task:

- Start incrementally - adhoc. eg: write a short story about {subject} ?
As you play with it add different/hard examples into your dataset.

- Use LLM's to help. Say you are building a essay evaluator. Then you can prompt into generate (essay,rating,explanation) pairs
- Add more data to users and take feedback (like/dislike or annotators)

Is there a way to quantify the quality..'test coverage' ?

## Metrics for LLM's

<img width="637" alt="Screen Shot 2023-06-14 at 11 46 29 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/a556a40d-6601-4ea0-8e7d-214ca0c4d3a6">

Reference matching metrics: a.check for semantic similarity b. ask another llm if two answs are factually consistent.

Which is better: ask an LLM which one is better acc to any criteria.

Can we automate evaluation ?

Improving the output in production

- self-critique(gaurdrails.ai)
- sample many times

-----------
## State of GPT


1. Can't do too much reasoning per token : sol: chain-of-thought.
2. Sample multipletimes

<img width="1318" alt="Screen Shot 2023-06-14 at 12 53 20 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/18d791d2-e39e-4779-b76d-8ce7847c3d0e">


<img width="1288" alt="Screen Shot 2023-06-14 at 1 19 56 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/ae138c35-3a0f-4e78-a64c-944ac938ded0">



<img width="1081" alt="Screen Shot 2023-06-14 at 1 22 30 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/e64892a3-08c9-4a66-9c92-ddba5504ff61">

<img width="876" alt="Screen Shot 2023-06-14 at 1 18 53 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/61025ce1-b150-49e2-8cbc-606fd83ff5fa">

--------------


## Augmented language models

llm good at : basic reasoning,language/code understanding,instruction following.
bad at : factual check/upto date knowledge,interacting with world,more complex reasoning.

Context: give unique, upto-date knowledge
GPT-4: Around 30k tokens(college thesis)

right context = information retrieval.

embeddings = use your task and get an embedding.

baseline = sentence transformer(eg:Sentence-BERT: Sentence Embeddings using Siamese BERT-Networks)
SOTA = One Embedder, Any Task: Instruction-Finetuned Text Embeddings
https://huggingface.co/docs/transformers/model_doc/longformer

Now approx nearest neighbor search.

problems
- your queries and search are not same
`sol`: https://blog.reachsumit.com/posts/2023/03/llm-for-text-ranking/

- you might have some structure to your data
`sol`: lamaindex

Hallucinations:

- ensemble.
- simulate a personality
- prepend - my best guess is .

## RAG based models:

for training

<img width="638" alt="Screen Shot 2023-06-12 at 10 17 15 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/f5d141d2-79a0-4dd9-8b30-d614a6265ac8">



knowledge intensive tasks: approach is retrieve and rerank.



<img width="1039" alt="Screen Shot 2023-06-12 at 10 15 16 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/c55b451d-cce4-41fb-b064-20a7ad2e7618">


- Fact checking


--------------------
## future of LLM's

Models unlikely to get significantly bigger as (scaling laws) need more data and we are gonna exhaust.
- already ran out of highquality data(internet)
- FLOPS = (#TOKENS*#PARAMETERS) (both have equal impact, we are scalin models too quickly)

Typically we only train one-epoch.

-----------------------------------------
## Attention and transformers 

Human visual attention allows us to focus on a certain region with “high resolution” (i.e. look at the pointy ear in the yellow box) while perceiving the surrounding image in “low resolution”.attention in deep learning can be broadly interpreted as a vector of importance weights

What's wrong with seq2seq?

- disadvantage of this fixed-length context vector design is incapability of remembering long sentences. Often it has forgotten the first part once it completes processing the whole input.

- 
