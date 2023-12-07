# Round 1: ML Fundamentals

## Datasets

1. How does one collect data and prepare a dataset for training?

   collect: let's consider a supervised task
    - Decide the features we need and the stability of those.
    - Sampling: It should reflect the production scenario.
      
   prepare:
    - preprocessing = min-max scaling, stand, binning, etc
    - feature engineering = generate features

  2. What are some common problems in collecting data for training a model?
     
     - Lack of ground truth labels
     - Noisy features

  3. How to determine whether collected data is suitable for modeling ?


  4. what are some ways to deal with label imbalance in the dataset?

     - Dataset: Rebalancing by sampling with replacement.
     - Loss function: Adjust the loss function. Eg:1.focal loss 2. weight the loss by 1/#class.

  5. How to deal when labels are missing ?

      
     
