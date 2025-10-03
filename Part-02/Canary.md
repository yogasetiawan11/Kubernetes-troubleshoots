# why canary

<img width="550" height="415" alt="Image" src="https://github.com/user-attachments/assets/5c0bdfaa-cb9b-4328-9430-6c647c68d795" />

In canary you actually deploy a new version on to production by allowing percentage people to access It. example:
Let's say you have 100 users with canary you can tell Load Balancer of the production cluster that "only 10% of traffic forward to latest version and forward 90% of traffic to old version. 

using canary model you can test latest version of your application to real users and for unlimited amount of time, you can do it for a day, a week or even a month. 

If you feel comfortable with the latest version you can give 100% of the traffic, so in canary you have more control through the Load Balancer.