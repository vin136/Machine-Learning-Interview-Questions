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

**What is Bias and Variance tradeoff ?**

It's an approach for capturing the generalizability of the algorithm. Another alternative would be VC analysis.

Bias => captures how well we can approximate the target function with your model(hypothesis) class

Variance => Captures how hard it's to zoom in on the best model or weights. How much does your model change between different samples from the population?

Thus there is a trade-off, if we start with a complex model class, we have low bias but it's hard to pick or zoom in the optimal model due to variance.

In the squared error setting it decomposes nicely into (bias^2 + variance + error).

**How do you handle Categorical Variables ?**


**How do you handle missing values?**

Try your best to identify the cause of missingness

MC


**Diagnostics**


Identify issues and do appropriate actions:

Performance breakdown

Gap btw Human-level and train => proxy for Bias
train and valid => proxy for variance

Gap valid- test => maybe dist.mismatch. Generally Valid/train data comes from different process, say some open-source data and your test set a the best reflection of the deployment scenario.(due to cost)

Gap btw test-deployment => generally due to overfitting on test-set. (checked multiple times)

`perf-berakdown`: Human-level performance/error (proxy for irreducible error),Train-perf,Valid-perf,Test-perf,Deployment-perf

High bias problem: 

1. Increase flexibility of the model(use more features, introduce non-linearities, complex kernels, reduce regularization

High Variance problem:

1. Decrease flexibility, Add more data

How to know if my Data is enough ?

Plot between fraction of data vs Validation error.

1. Gap is low and both the values are far off from desired limit => High bias (more data likely not help)
   
2 . Gap is high and they don't look flattened, and valid error is higher than train => get more data.

# ML Algorithms(classical)

Classification

Multi-class => Softmax classification

Multi-label => output label is a binary vector (1,0,0..). Can be treated as a modified version of logistic regression $p(y|x,\theta) = \pi_{c=1-c}Ber(y_{c}|w,x)$.

`Kernel Trick`:

General purpose technique to transform the problem whose complexity depends on #features to #data-points. Whenever the optimization problem can be reformulated with dot-product between data-points we can possibly apply kernel trick. Useful if the feature space is high/infinite dimensional.

Linear regression can be kernelized. => here we store beta's (#samples).
SVD => The beta vector for SVD is typically sparse thus need not store all the data points. Thus quite effficient to kernalize SVD.
PCA => can also be kernelized.

1. Logistic regression
   `complexity`=> train time O(nd).
   `Background`: Minimize the log-likelihood of the Bernoulli model. It's convenient as the loss is convex.
   
   In the underlying model, we assume random noise whose CDF is the logistic distribution. By varying this assumption we can get various models `GLM`. Also has max-entropy interpretation.

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

`sigma` => controls the peakedness of the Gaussian kernel. small `sigma`=> more peaked => overfits.

The SMO algorithm => train complexity O(n^2).

3. Linear Regression: Orthogonal projection of Y onto the column space of X.
   Note: if p>n or if columns or collinear => not invertible,no X.tX=>not invertible.

   Ridge-regression
   <img width="600" alt="Screen Shot 2023-07-07 at 6 01 19 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/34a30893-cb8b-47ec-8af5-4cf5a92f46a0">

Bias-variance tradeoff

<img width="600" alt="Screen Shot 2023-07-07 at 6 03 57 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/c3e32bdc-1dc8-40dc-b18b-87999832e8d2">


Robust Regression: Can model the noise as student-t or Laplace dist, thus accounting for outliers.

   <img width="600" alt="Screen Shot 2023-07-07 at 5 54 12 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/3eeb53c0-e565-44d5-b5e9-f01acac4d17d">

    
4. Trees, Random Forests, Boosting
   
   Bootstrap-aggregation => almost same bias and reduce variance (random forest => bootstrap agg + column sampling)
   Objective => Classificaton(entropy or gini-impurity (1-sum(p_i^2). Tries to make each leaf pure. Regression(MSE/MAE etc)

   Feature importance => total reduction or gain at each node(`con`: tends to give high values to features with high-cardinality), Permutation feature importance (slow but less biased).

   **Boosting**
   

6. Dimensionality reduction
   
   `USES`
   - Can detect possible clusters
   - Remove noise or highlight potential outliers
   - As a preprocessing step => clustering, classification/regression

   Core Idea in SVD : Any matrix can be decomposed into sum of rank one matrices.
   
<img width="810" alt="Screen Shot 2023-07-07 at 4 05 44 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/32b9083f-b46b-49b9-a2d7-433c2ef2aa50">

<img width="600" alt="Screen Shot 2023-07-07 at 4 07 33 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/0c1b8c9d-7569-4848-b9b4-1008ca6136a7">

<img width="600" alt="Screen Shot 2023-07-07 at 4 08 08 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/83ba6001-3d20-468a-9033-eaad3d8174a4">

PCA

- Find directions of maximum variance
- Preserve pair-wise distances


<img width="600" alt="Screen Shot 2023-07-07 at 4 10 07 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/39baf9bb-6533-4ee0-a099-e2ae79e3cf55">

   
**How to pick the dimensionality in PCA**

ELBOW-METHOD, on explained variance.

Can't pick `dim` that minimizes the reconstruction error on the validation set as that would lead to using all the dimensions.
   

7. Clustering

Model-free Methods:


K-means:

**Assumptions**

- All clusters of same size,variance
- Also assume convex shapes
- All clusters have same prior probability
- Distances should be given.

Objective: 
- Minimize the within-cluster sum of squares from the center.
- (OR) Minimize paired mutual distances between points in a cluster.

Tries to jointly find both the cluster centers and cluster assignment and thus can't be exactly optimized. Lyod's algorithm gives an approx solution.

   <img width="600" alt="Screen Shot 2023-07-07 at 4 21 34 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/e0204c23-76c8-41d8-8c4e-bc85f5c7f50d">

In practice we use `kmeans++` that assigns clusters as far as possible. This gives a bound on how worse the algoritm can be. Lyods' can give arbitrary bad clusters.

       
Gaussian Mixture model:

Generative model

- Takes into account of the variability of each cluster,thus instead of computing the dist,  we scale it down by the variance
- Prior probability takes into account of the cluster size, thus a point is not just assigned to closest one.
- A point is assigned to all clusters given by distribution.

  Optimized By EM

We can pick the k that maximizes the log-likelihood of the data+ penalizes for the complexity of the model.

BIC(K) = log p(D|θˆk) − D_K log(N), (D_K=> #PARAMS in the model)

** How to choose K in k-means ?**

NOTE: WE CAN'T JUST USE validation, as just using more k also decreases validation loss inevitably.

**Heuristic method**

Plot silhouette coefficient for diff k, at pick the one at the elbow.(at the `kink`)

`silhouette coefficient of an instance i to be sc(i) = (bi − ai)/ max(ai, bi), where ai is the mean distance to the other instances in cluster ki = argmink ||μk − xi||, and bi is the mean distance to the other instances in the next closest cluster, ki′ = argmink̸=ki ||μk − xi||. Thus ai is a measure of compactness of i’s cluster, and bi is a measure of distance between the clusters. The silhouette coefficient varies from -1 to +1.`
   
   

# Deep Learning

Revise [1](https://jupyter.cs.rutgers.edu/user/vk405/lab/tree/Projects/ML-review/nbs/DeepLearning.ipynb), [optimizers](https://github.com/vin136/Machine-Learning-Interview-Questions/blob/main/nbs/Deep_LEARNING_NOTES.ipynb)






