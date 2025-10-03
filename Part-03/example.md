# First
you create simmilar environment in the same kubernetes cluster such as:
you have 4 replica for version 1 and also 4 replica for version 2. all you need to do just edit the Ingress resource so Load Balancer can forward the trafic to service 2.
## Edit the Ingress
```bash
kubectl edit ing <name-ing>
```
The output will diplay like this:
```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"networking.k8s.io/v1","kind":"Ingress","metadata":{"annotations":{"nginx.ingress.kubernetes.io/rewrite-target":"/"},"name":"minimal-ingress","namespace":"default"},"spec":{"ingressClassName":"nginx-example","rules":[{"http":{"paths":[{"backend":{"service":{"name":"test","port":{"number":80}}},"path":"/testpath","pathType":"Prefix"}]}}]}}    nginx.ingress.kubernetes.io/rewrite-target: /
  creationTimestamp: "2025-10-03T13:45:13Z"
  generation: 1
  name: minimal-ingress
  namespace: default
  resourceVersion: "86986"
  uid: c9881b30-23eb-4a3a-aac3-937c9fda26bb
spec:
  ingressClassName: nginx-example
  rules:
  - http:
      paths:
      - backend:
          service:
            name: test  # service pointing
            port:
              number: 80
        path: /testpath
        pathType: Prefix
status:
  loadBalancer: {}

```

you will see in the Ingress resource you have ``service`` pointing. In this service path you can define the service you wanna use. example the service above pointed to ``test`` service if you just change It to canary and It will forward the traffic to ``canary service``. 