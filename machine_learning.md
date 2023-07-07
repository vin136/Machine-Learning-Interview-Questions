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



**1. How to deal with class imbalance ?**

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
      
   
# ML Algorithms(classical)

Classification

Multi-class => Softmax classification

Multi-label => output label is a binary vector (1,0,0..). Can be treated as a modified version of logistic regression $p(y|x,\theta) = \pi_{c=1-c}Ber(y_{c}|w,x)$.

`Kernel Trick`:

General purpose technique to transform the problem whose complexity depends on #features to #data-points. Whenever the optimization problem can be reformulated with dot-product between data-points we can possibly apply kernel trick. Useful if the feature space is high/infinite dimensional.

Linear regression can be kernelized. => here we store beta's (#samples).
SVD => The beta vector for SVD is typically sparse thus need not store all the data points. Thus quite effficient to kernalize SVD.

1. Logistic regression
   `complexity`=> train time O(nd).
   `Background`: Minimize the log-likelihood of the Bernoulli model. It's convenient as the loss is convex.

   **Your model is not performing well, what will you do ?**
   ans:
   - L1/L2 regularization (Regularization as a MAP estimate with L2: gaussian prior)
   - Check if features are normalized and are on the same scale. (good for optimizers and also when applying regularization, we implicitly assume all feats to be on the same scale).
   - Few Outliers might be affecting the decision surface unduly. eg: maybe due to label noise(some points are mislabeled)
  
     **How to deal with outliers in case of logistic regression ?**
     ans:
     - Fit and remove ($W^{T}x$ gives dist from hyperplane) iteratively.
     - Model noise: mixture model => label is sampled from a random-model(Bernoulli) or from a conditional model(logistic regression)
    
2. SVM

margin = sum of the distance between the closest +ve and -ve points from the decision boundary.
Objective = maximize the margin, to account for imperfect classification we introduce slack variables. Constrained optimization problem => projected gradient descent.

Can also be interpreted as `minimizing hinge loss` along with l2 normalization on weights.

`DUAL FORM`: In dual form we can convert it to maximization problem(Lagrange multipliers) and note that now we can kernalize the model. Only relies on dot-products between points.

`C` => 1/c*(regularization). Inc C => dec regularization,makes the model overfit, dec C => more regularization

`sigma` => controls the peakedness of gaussian kernel. small `sigma`=> more peaked => overfits.

The SMO algorithm => train complexity O(n^2).

4. Linear Regression

   
    
2. Trees, Random Forests, Boosting

   
       




   
   











