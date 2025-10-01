# Kubernetes-troubleshoots
# why do you need this?

Let's you are working in e-commerce you have a service called as a ``Payment``. V1 of this Payment have 4 replicas, Basically 4 copies of version 1 are deployed as a deployment on Kubernetes Cluster. 
After working for a month team developer has come up with new version of this app.
Now your development team along with QE, and management has decided to upgrade the version of ``Payment`` from V1 to V2. Let's you don't follow deployment strategy and you simply delete or remove the version V1 from Kubernetes cluster. assume it takes 5 minutes, once the uninstallation is successful and verify the uninstallation is done. Now you'll go to Cluster and Install new version (V2) with 4 copies (replicas). Now assume for this 4 copies to be ready to serve the traffic might be It takes another 5 minutes. 

Overall this 2 process of Uninstall and Install the New version take 10 minutes. that means ``Payment`` service is down for 10 minutes, so can you Imagine how much is it for organization like Amazone, Google etc If their services down for 10 minutes

If this things happened It causes 2 Big impact for organization,
1. Definetely is Revenue lost 
2. Bad Users experience which affect reputation of the organization 

If you don't follow the deployment strategy the issue that you would see is downtime, to reduce the downtime or even it's possible make it become zero downtime during upgrade of your deployment using ``deployment strategy``.