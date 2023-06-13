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

## RAG based models:

for training

<img width="638" alt="Screen Shot 2023-06-12 at 10 17 15 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/f5d141d2-79a0-4dd9-8b30-d614a6265ac8">



knowledge intensive tasks: approach is retrieve and rerank.



<img width="1039" alt="Screen Shot 2023-06-12 at 10 15 16 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/c55b451d-cce4-41fb-b064-20a7ad2e7618">


- Fact checking




-----------------------------------------

Chartbeat

Why Chartbeat ?

- I'm interested in working in ML-centric companies
- I think that for most of these companies Engineering is the stronger bottleneck than ml.
So to my knowledge, chartbeat works in small teams and offers good combination of ml+engineering problems.

Questions ?
1. what is one problem that the team is facing now and would delighted if i could add some value/improve ?
2. I read the company blog, and it talks Headline optimization,that implies you did A/B testing for the company. Ultimately users want actionable steps,not just analytics,this implies full blown ml models and optimizing on them. How far the product offerings go in this direction ? so it boils down to having a ton of recommender systems,(eg: better ad placement personalization ), how to you manage all these ?
