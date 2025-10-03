Let's say you have V1 of Payment app In this V1 you have 4 Replicas, whenever developer teams want to deploy new app called as V2. Instead of Uninstalling V1 completely 

what Rolling update does that is when you go to Kubernetes cluster and If you Run a command 
```bash
kubectl set images
```

or

```bash
kubectl edit <your-deployment>
```

Then go to (``.spec .image``) and change the version from V1 to V2. instead of uninstalling the previous copies Rolling update will add one single copy first of Version 2 (V2) so basically It will add 25 percentage by default.


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
