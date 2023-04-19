
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




