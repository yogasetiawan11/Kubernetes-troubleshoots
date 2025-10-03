[You can refer to this Documentation](https://kubernetes.github.io/ingress-nginx/examples/canary/)


# Prerequisite for this use case 
create component below for different app (Version 1 and Version 2)
- Deployment
- Service
- Ingress

# Create a Main ``deployment`` and ``Service``
```bash
echo "
---
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: production
  labels:
    app: production
spec:
  replicas: 1
  selector:
    matchLabels:
      app: production
  template:
    metadata:
      labels:
        app: production
    spec:
      containers:
      - name: production
        image: registry.k8s.io/ingress-nginx/e2e-test-echo:v1.2.3@sha256:d85d82c957d4f01d9851f1d9cf37248b377b4a954fa17b58beb089e11cdebb30
        ports:
        - containerPort: 80
        env:
          - name: NODE_NAME
            valueFrom:
              fieldRef:
                fieldPath: spec.nodeName
          - name: POD_NAME
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
          - name: POD_NAMESPACE
            valueFrom:
              fieldRef:
                fieldPath: metadata.namespace
          - name: POD_IP
            valueFrom:
              fieldRef:
                fieldPath: status.podIP
---
# Service
apiVersion: v1
kind: Service
metadata:
  name: production
  labels:
    app: production
spec:
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
    name: http
  selector:
    app: production
" | kubectl apply -f -
```

# Create the canary deployment and service 

```bash
echo "
---
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: canary
  labels:
    app: canary
spec:
  replicas: 1
  selector:
    matchLabels:
      app: canary
  template:
    metadata:
      labels:
        app: canary
    spec:
      containers:
      - name: canary
        image: registry.k8s.io/ingress-nginx/e2e-test-echo:v1.2.3@sha256:d85d82c957d4f01d9851f1d9cf37248b377b4a954fa17b58beb089e11cdebb30
        ports:
        - containerPort: 80
        env:
          - name: NODE_NAME
            valueFrom:
              fieldRef:
                fieldPath: spec.nodeName
          - name: POD_NAME
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
          - name: POD_NAMESPACE
            valueFrom:
              fieldRef:
                fieldPath: metadata.namespace
          - name: POD_IP
            valueFrom:
              fieldRef:
                fieldPath: status.podIP
---
# Service
apiVersion: v1
kind: Service
metadata:
  name: canary
  labels:
    app: canary
spec:
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
    name: http
  selector:
    app: canary
" | kubectl apply -f -
```


# Create Ingress Pointing To Your Main Deployment
```bash
echo "
---
# Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: production
  annotations:
spec:
  ingressClassName: nginx
  rules:
  - host: echo.prod.mydomain.com
    http:
      paths:
      - pathType: Prefix
        path: /
        backend:
          service:
            name: production
            port:
              number: 80
" | kubectl apply -f -
```

# Create Ingress Pointing To Your Canary Deployment

```bash
echo "
---
# Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: canary
  annotations:
    nginx.ingress.kubernetes.io/canary: \"true\"
    nginx.ingress.kubernetes.io/canary-weight: \"20\"  
# Here you determine percentage traffic for your V2 or canary deployment
spec:
  ingressClassName: nginx
  rules:
  - host: echo.prod.mydomain.com
    http:
      paths:
      - pathType: Prefix
        path: /
        backend:
          service:
            name: canary
            port:
              number: 80
" | kubectl apply -f -
```

# testing this set up
```bash
for i in $(seq 1 10); do curl -s --resolve echo.prod.mydomain.com:80:$INGRESS_CONTROLLER_IP echo.prod.mydomain.com  | grep "Hostname"; done

# you can change this particular value INGRESS_CONTROLLER_IP with minikube IP
```

The difference between Main Ingress and canary Ingrees is that In canary Ingress you determine the percentage of traffic you want to set. Ingress controller will read both Main Ingress and Canary Ingress and tell the Load Balancer to distribute The traffic based on ``annotation`` that exist on Canary Ingress, above example I specify 20% traffic for latest version. 