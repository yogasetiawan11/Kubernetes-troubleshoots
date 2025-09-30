# Blue/Green 
It’s a deployment strategy used in software release to minimize downtime and risk. Blue Green is the most safest and costly Deployment strategy.

# Why Blue Green is the most safest

<img width="798" height="561" alt="Image" src="https://github.com/user-attachments/assets/e78ce11d-f141-4106-9490-e0c7922a5652" />

- R1–R4: Replicas (Pods/instances) running version v1 of your app.
- S1: Service that exposes those replicas.
- The Load Balancer is currently pointing to S1, so all user traffic goes to Blue.

In the Image above you have Blue side and Identical Green side (same number replica, simmilar service), with this mechanism you can change to the arrow from Blue side (V1) to Green side (V2) and vise versa. 

so If there is something error in Green side (V2) you can easily roll back to Blue side 

# Why this method is costly?
In the previous example, you're creating the same number of replicas, it's very costly affair. each Pods that you are created it will use memory, it will use CPU wether they are in used or not, Because once you switch Load Balancer from ``Blue`` to ``Green`` definitely you won't delete ``Blue`` side, Probably you will keep it for 5 days or even 10 days, so for every Replica it will consume resources (GPU, memory). imagine if you have a lot of services for each service you have duplicated amout of Pods.

Conclution:
You have two identical environments:
Blue (current/live environment) → where users are connected right now. Green (new environment) → where you deploy the new version of your app. At any given time, only one environment (blue or green) serves live traffic.
