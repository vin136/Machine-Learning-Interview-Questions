
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


### Imbalanced data sets: can happen both in regression(predict hospital bill,here 95 percentile might be most important.) and classification.



1. Metrics: class-wise avg'd stats(accuracy of F1 score) or AU PRC


2. Data level: use appropriate metrics.

Empirical: found that complex models(nn's ) less impact compared to simple ones like linear regression.

- Resampling naive: Oversampling(overfitting) , Undersampling(missing out on data)

- Better (for low dim)

   - undersampling : One interesting method for low dimensional undersampling is `tomek links`: find pairs of points that are from opposite classes and remove the majority class point.
   - oversampling: Create new samples: `SMOTE`(oversample through convex combination of data-points.







# Training-data generation with GAN's

Problem: CT SCANS segmentation WITH SOME Contrast and non-contrast images.

Here since the ground truth labeling remains same for both the Constrast and non-contrast images. Also found it to work well on out-of-sample : say scans from different hospital.

Used CycleGAN : conditional GAN.

Active learning approaches:

1. 100 train samples with labels, rest 900 unlabeled. Train on 100 samples and based on confidence of the predictions in the rest, decide to label them. This assumes the model is well-caliberated.


