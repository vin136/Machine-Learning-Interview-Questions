S:situation,T: task , A: action ,R: result

1. Tell us about yourself ?`(connect how your past ex connects to present role)`

ans: Hi, this is Vinay. I've graduted with a masters in computer science specializing in machine learning from rutgers. The highlight was i worked as a part-time lecturer for various
courses, especially `machine learning principles`. I designed homeworks and took weekly classes. not only it deepened my understanding but learned to effectively convey complex ideas. Before that
I worked as a machine learning engineer in India for 3 years. It was a small fast-paced team and I'm responsible for building end-end deep reinforcement learning based trading strategies. I'm proud to have taken the company along with my team from seed
stage to a thriving profitable one. I'm looking for " "(company + role)

Situational Questions:
`CONFLICT`

1. Dealt with particularly difficult co-worker / situation?

ans: 
situation: While working at Niveshi, we had a custom front-end to analyze our experiments. It was built in ruby by our CEO when the company was started. But as our requirements
grew, and kept on adding asking for more features, bugs kept on increasing : from system fetching wrong results to ocassional crashing. I realized that's because placing too much proirity on `speed` over `quality` and also
having too many things on his plate.

task:  I wanted those responsiblities to be handed over to other engineer who would rewrite it in python(as he's not well-versed in ruby and we realized adding a lang dependency is unnecessary). We did'nt agree on it as i was thinking about efficiency in the long term vs short term cost of effort and maybe slowing down a little bit.

action: We decided we'll create the functionality on the side and present it to both the CEO and CTO. This time we'r proactive as we already implemented a good chunk. Also stressed on how this free up his time which is much more valuable to the company at that stage.

result: It worked. That engineer hired an intern and completed the task. Eventually he too responsibilyt of the operations part.

------------

`Leadership and Influence`

2. You made a decision that was unpopular and how did you go about implementing it ?
3. You accomplished something in your career that made you proud - and why was that moment meaningful ?


ans: 

Situation: At niveshi, we were building deep rl based trading system. The premise was that we should try to automate everything in the process of making a trading strategy compared to convential quant setting where you try to come up with signals and then build rules on the top for entry and exit. this is a long process and not scalable as it's specific to each asset.

Task: We started out by trying to build a Deep RL based system. After some initial success we are having trouble generalizing the approach to other asset-classes. I decided to build supervised models(still using deep learning). But i need to show some initial evidence that this idea works before asking for a full blown efforts.

WHy ?
Additional complexity of modeling as RL is not justified by the problem.

Action: Now building a complete supervised based model from scratch (hyperparameters, architecture etc) is a long process. Also markets are very efficient and typically we need to work harder than in the classical setting. What are the minimum changes i can make to the system and be able to verify the merit of the idea ? I added an additional output to the system that also predicts the price-change along with the RL action. Somehow combine this output with the RL actions to improve the models over and above their current performance. I did this and this turned out to be `the change`, that changed everythnig for us. Using this idea we covered all the asset classes in india + i was made the lead ml engineer and now can hire engineers under me.

bigtakeaway : I took the initiative and was proactive in my actions. 

Why it's meaningful: Also this is not the only idea that i persisted in. But most often they don't work. It shows the value of grit.

----------------

`Time and Resource Management`

You are on a tight deadline, how did you handle it ?

ans: `Youtube annotation`


# Project Walk-through Questions




