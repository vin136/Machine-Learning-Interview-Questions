
### Metrics aside.

precision = TP/(TP+FP),

recall = TP/(TP+FN), and F1.

These are assymetric metrics.

Sensitivity = recall +ve
specificity = recall -ve.

measure the effect of ordering/descriminative power btw classifiers.

plot btw sensitivity vs specificity = ROC CURVE (random point from +ve, -ve, what's the prob that +ve has higher probability)
plot btw sensitivity(recall vs precision) = PR curve.(stops at prevalence)



logloss on validation => much better as it takes GM and one confident bad prediction has very strong effect.(heavily penalizes confident bad predictions)

--------------------------



### How to handle lack of labels ?

1. Weak supervision : heuristic rules to label. eg: NLP sentiment : `if get rich fast` => spam. Encoding  domain expertise. Even if giving for hand-labeler, better to programmatize it.

Success stories : for radiologist chest x-ray. worked almost as well.

2. semi-supervised methods:

start with the labels you have and get predictions on others, take the most confident ones and train on them.

3. Transfer learning.: unsupervised pretraining.

4. Active learning: what data-points i should label that will give me most bang for the buck.

Active learning approaches:

1. 100 train samples with labels, rest 900 unlabeled. Train on 100 samples and based on confidence of the predictions in the rest, decide to label them. This assumes the model is well-caliberated.
2. disagreement btw different classifers



### Imbalanced data sets: can happen both in regression(predict hospital bill,here 95 percentile might be most important.) and classification.



1. Metrics: class-wise avg'd stats(accuracy of F1 score) or AU PRC


2. Data level: use appropriate metrics.

Empirical: found that complex models(nn's ) less impact compared to simple ones like linear regression.

- Resampling naive: Oversampling(overfitting) , Undersampling(missing out on data)

- Better (for low dim)

   - undersampling : One interesting method for low dimensional undersampling is `tomek links`: find pairs of points that are from opposite classes and remove the majority class point.
   - oversampling: Create new samples: `SMOTE`(oversample through convex combination of data-points.


For neural networks or high-dimensions: 

getting stuck/ cheating => can get low loss even by ignoring a class thereby fitting on to non-generalizable patterns.

One technique: Two phase learning => first train on rondomly undersampled data (avoids simple patterns), then can finetune.

dynamic sampling: throughout your training you can oversample underperforming class and undersample overperforming class.

3. Algorithmic level

a. Cost Sensitive learning(give cost to each class): directly model costs.
b. class-balanced loss: just give inverse class frequency as weight
c. Focal loss: -(1-p)^g log(p)


### Limited training data

In the order of simplicity:

1. DATA AUGMENTATION: Label preserving transformations.(nlp and cv)
2. Mixup: train on convex combinaiton of samples and labels.
3. Data synthesis:

Creative uses of GAN's:

`Training-data generation with GAN's`

Problem: CT SCANS segmentation WITH SOME Contrast and non-contrast images.

Here since the ground truth labeling remains same for both the Constrast and non-contrast images. Also found it to work well on out-of-sample : say scans from different hospital.

Used CycleGAN : conditional GAN.

# Continual Learning

Under new data : retraining vs finetuning.

Finetuning is little bit hard : catastrophic forgetting. By definition we want to use new data as it might be different from train and has new information,this poses catastrophic forgetting..aka forgetting old knowledge. Thus we retrain.

Why continual learning:

- Data slowly changes: take that into account.
- ML on rare events: say predicting sales on the black friday. very few historical instances and they are very far apart(1 yr). Need to have a systems that can adapt throuhout the day.
- In recommender systems we have `continual cold start problem`: a).we get new users signing up b) maybe even same user switches the device (mobile to laptop).

Challenges

`Fast Data Access`: we got to get data directly from streaming systems(eg:apache kinesis)(that take data from apps and dump in data-warehouses(big-query,amazon-redhift). Next we need labels. Unless there are natural labels (stock prices,recommender systems) we might have to label them. even nl case, we typically store logs and need labeling., eg:user bought/clicked this item, we still need to identify the query and label it.(hit/miss)

`Evaluation`:

If we update so often more chance of error or even adversarial attacks. So times for problems require A/B testing,eg: credit fraud detection , to properly test it out got to wait out until you get enough fraud's. 

`Algorithmic considerations`

Neural network based: can easily update

Collaborative/matrix based or Tree based models: even adding small data necessiates updating by taking all the data. Not very friendly for `collaborative filtering`.

How often to retrain ? Estimate the value of new-data. (can be used to answer if we can retrain before hitting stop-loss)


<img width="661" alt="Screen Shot 2023-04-21 at 3 35 31 PM" src="https://user-images.githubusercontent.com/21222766/233720378-8647f736-0017-48f3-9e09-d0555854b58d.png">

Evaluating your models

Offline Evalaution : hopefully you did a good job here.

`Online Evaluation`

1. Shadow deployment : Deploy the `candidate model` along with the `exiting model`. route each request to both the models.

(maybe your new model is faster, latency improvment, thus better to gauge in real time @niveshi)

2.Canary release: deploy it to only small population and gradually rollout.

3. A/B testing: cons(opportunity cost / data efficiency)

- 3.1 Interleaving experiments: uses the same users(reduces variance) and thus need less data. We can interleave results from two algorithms, say search rankings.
- 3.2 Bandits: Can we dynamically route traffic based on our updating confidence on bandits. (more data efficient)
- 3.3 Contextual Bandits: Here we take a more granular look and optimize at the level of actions. Say which of the 100 items to show. (balance exploration vs exploitation)

