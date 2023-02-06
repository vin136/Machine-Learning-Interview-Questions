<details><summary>
  1. In 2006 the New York Times published a list of the safest and least safe cities in the U.S., based on per capita car accident data. The 
10 largest cities did not appear on either list. What might explain this phenomenon?
  

Larger cities have more variation in their data.

Smaller cities have more traffic laws.

Smaller cities have more variation in their data.

Larger cities have more traffic laws.(source: brilliant.org)</summary>
  

  ** Small data sets have more variation than larger data sets. There might be some small cities with virtually zero accidents per capita, and some small cities might have many accidents per capita.

But cities with large populations would show much less variation. The data set is so large that the results will always tend towards the mean. ** 

</details>

<details><summary>
  2. You developed a new machine learning model and compared it's performance with an old model. You retrieved it's performance on 3 sub-groups of the population, say based on educational background. It turns out that your model is worse(accuracy) in each of these groups but better overall. Your population remained same, just that your old model is from users in 2019(when that model was live) and the new model is from the current year. How would you explain this or what will you do ?</summary>
  

  ** This is an instance of [simpson's paradox]([https://hello.ca](https://en.wikipedia.org/wiki/Simpson%27s_paradox)). Whenever we are comparing between sub-groups,it's important to also look at relative proportion in each of these groups. It might so happen that when the new model is deployed, people have changed their group identity.(some might have acquired a college degree). This happens in many practical situations/deployments.(when you bin your users into groups - subscription status,usage etc)** 

</details>




