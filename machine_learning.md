# Metrics


---



1. Classification metrics

Precision


recall

ROC Curve and PR curve

ROC => X,Y: Sensitivity(pos recall),Specificity(neg recall), curve only descends or either stays flat. Area=> prob that a point from +ve class has higher prob value comp to -ve class(randomly chosen).

`how to plot`: Divide each axis into #samples in that group: x-axis(sensitivity) equal intervals of #positive samples and y-axis(specificity) into #negative samples. Now take the rank ordered predictions, and as we encounter a pos sample move right and for neg sample move down.

PR => X,Y => Recall, Precision.

Area under PR => Avg. precision (by randomly picking the threshold, what's the precision)


`CONS`
Both only look at rank-ordering not the actual values. Better metric to compare different models => Logloss(highly penalize confident wrong predictions), Gain.

Metrics for class-imbalance => Accuracy < AUROC< AUPRC

But it's best to calculate metrics per class and set thresholds.


2. Recommender Systems metrics


------------------------

# Real-world ML Probs/Solutions



1. How to deal with class imbalance ?

`Why it's Bad ?`

- Insufficient signal for the model to detect minority class
- Loss can get stuck in local optima with bad perf on the minority class
- Often in practical situations class imbalance implies we have an asymmetric cost for error. eg: detecting cancer, here recall matters more than precision, but your model treats all the same, thus important to configure the loss appropriately.

`Methods`

1. Metrics-level
   - eg: We can use avg.F1 calc. per each class in the `one vs Rest` setting.
2. Data-level
   - Resampling(over and undersampling)
   - SMOTE(linear combination of the points to generate new samples): good for low-dimensional data only.
  
     Heuristic/Research approaches:
      - Two-phase learning: (training a gradient-based model)
         a. Train on class-balanced sample:(`n` samples from each class) => loss function is not biased and learns the ways to distinguish each class.
         b. Fine-tune on the original data: Feed the natural population information.
     - Dynamic sampling: After each round, measure the perf on each class, oversample under-performing class.
    
 3. Algorithmic approaces
    - Cost-Sensitive learning: If we have a cost-matrix $C_{ij}$,cost of classifying `i` as `j`,our loss is weighted avg.(weighted by the clsf's predicted probability)
    - class-balanced loss: reweigh the loss proportional to the `inv of #samples in that class`
    - Focal loss: teach the model to focus more on difficult classes
   











