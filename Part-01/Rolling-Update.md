# What is rolling update

<img width="658" height="400" alt="Image" src="https://github.com/user-attachments/assets/5a505068-dedc-47da-92f5-5c3eedcb4edc" />

Let's say you have V1 of Payment app In this V1 you have 4 Replicas, whenever developer teams want to deploy new app called as V2. Instead of Uninstalling V1 completely 

what Rolling update does that is when you go to Kubernetes cluster and If you Run a command 
```bash
kubectl set images

or

kubectl edit <your-deployment>
```

Then go to (``.spec .image``) and change the version from V1 to V2. instead of uninstalling the previous copies Rolling update will add one single copy first of Version 2 (V2) so basically It will add 25 percentage by default this V2 version will replace 1 replica from V1.



Output you will see
```bash
spec:
  progressDeadlineSeconds: 600
  replicas: 4
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: nginx
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:v2        # Here you add :v2 to tell cluster that this is is the v2 of your app 
        imagePullPolicy: Always
        name: nginx
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```

Rolling updates will Create one new pod with V2 (because by default maxSurge = 25%). If you had 4 replicas of V1, Kubernetes temporarily adds a 5th pod running V2. Once that V2 pod is ready, it terminates one V1 pod (maxUnavailable = 25% by default). It repeats this process until all V1 pods are replaced by V2 pods. 

This is How you are using Rolling update, even if something goes wrong you have old copies (4 replicas) that still working so users will not see downtime, If everything goes well The latest version will replace all old version.