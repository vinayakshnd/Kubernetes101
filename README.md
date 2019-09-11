# Kubernetes101
Study material for Kubernetes


## Pre-requisits 
1. Create an account on Google Cloud which gives 300$ credits to use. 
2. Refer to [artical](https://medium.com/nycdev/deploy-to-a-managed-kubernetes-cluster-on-google-cloud-894d35b83619) to create a GKE cluster.
3. Fetch kubeconfig locally and setup/install kubectl.

## 1. Creating Pods

Pod is smallest atomic unit of work on Kubernetes.
Create bellow `1-nginx-pod.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: nginx
    env: prod
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
```
kubectl apply -f 1-nginx-pod.yaml
```
To list pods,
```
kubectl get pods
```

## 2. Creating Services

Create services to access pods. It's an abstraction which defines a logical set of Pods.
Create bellow `2-nginx-pod-service.yaml`. 

### ClusterIP Service
If no type specified, then by default `clusterIP` service is created.

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-clusterip-svc
spec:
  selector:
    app: nginx
    env: prod
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```
```
kubectl apply -f 2-nginx-pod-service.yaml
```
### NodePort Service

Notice the type here:

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport-svc
spec:
  type: NodePort
  selector:
    app: nginx
    env: prod
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```
```
kubectl apply -f 3-nginx-pod-nodeport-svc.yaml
```
To list services,
```
kubectl get services
```

## 3. Create Replica Sets

ReplicaSets always ensure the desired number of pods are running.
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
      env: prod
  template:
    metadata:
      labels:
        app: nginx
        env: prod
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

```
kubectl apply -f 4-nginx-rs.yaml
```
Again list the pods and kill one of the pod using bellow command,
```
kubectl delete pod <pod_name>
```
and verify that Kubernetes automatically creates a new pod.

License: [CC-BY](https://creativecommons.org/licenses/by/3.0/)
