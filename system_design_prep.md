# Design Youtube

Core functionality

Functional : user's upload, and be able to watch. (let's focus on these)

Non functional
 - Durable(storage of videos)
 - Scalability(about 1B dau), lets a ratio of reads/writes = 100:1,let a user watch 5 videos/day => (1/100)*5Billion are uploaded per day = 50 Million. Maybe most videos don't get views
 - Availability(always should see the feed) vs Consistency(may miss some subsscription video)
 - Minimize latency as much as possible.

User uploads a video => API upload(format,uid,metadata)
Storage => MongoDB (video document, user document, denormalized aka replication, this means when a user changes to say profile pic both the user doc and the video doc's need to be changed, here lag is fine and we can do asynchronous updates.)

For viewing => 1. we can send in small chunks.,2. how to send chunks=> tcp vs udp,tcp is better unlike live streaming where missing a frame is ok.
We need rate-limiter(inside load balancer) interms of uploading video's.

<img width="868" alt="Screen Shot 2023-06-23 at 9 24 13 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/5427d67a-83ce-4622-8c93-ecb736fee31c">
