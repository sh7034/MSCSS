사용자의 요청에 따라 각기 다른 파드로 연결해주는 오브젝트

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.15.1/deploy/static/provider/baremetal/deploy.yaml
```

#### Bare metal
물리적인 구성요소만
#### 예제
http 접속에 적용할 ingress
/pic로 접속시 nginx 파드로,
/mov로 접속시 httpd 파드로 연결
###### `ingress.yml`
```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata: 
  name: test-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - http:
      paths:
      - path: /pic
        pathType: Prefix
        backend:
          service:
            name: svc1
            port:
              number: 80
      - path: /mov
        pathType: Prefix
        backend:
          service:
            name: svc2
            port:
              number: 80
```

##### 예시 1 (kubectl 명령어)
```sh
kubectl apply -f ingress.yml --namespace ingress-nginx --dry-run=server

kubectl create deployment nginx --image nginx --replicas 1 --namespace ingress-nginx
kubectl create deployment apache --image httpd --replicas 1 --namespace ingress-nginx
kubectl expose --name svc1 deployment nginx --port 80 --namespace ingress-nginx
kubectl expose --name svc2 deployment apache --port 80 --namespace ingress-nginx
```

##### 예시 2 (yaml)
###### `nginx-dep.yml`
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dep-nginx
  labels:
    app: nginx
    env: prod
spec:
  replicas: 3
  selector:
    matchLabels:
      tem: nginx
  template:
    metadata:
      name: tem-nginx
      labels:
        tem: nginx
    spec:
      containers:
      - name: n1
        image: nginx
        imagePullPolicy: Never
        ports:
        - containerPort: 80
```
###### nginxsvc.yml
```yml
apiVersion: v1
kind: Service
metadata:
  name: svc1
  labels:
    env: prod
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
  selector:
    tem: nginx
```
###### apache-dep.yml
```yml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: dep-apache
  labels:
    app: apache
    env: prod
spec:
  replicas: 1
  selector:
    matchLabels:
      tem: apache
  template:
    metadata:
      name: tem-apache
      labels:
        tem: apache
    spec:
      containers:
      - name: h1
        image: httpd
        imagePullPolicy: Never
        ports:
        - containerPort: 80
```
###### apachesvc.yml
```yml
apiVersion: v1
kind: Service
metadata:
  name: svc2
  labels:
    env: prod
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
  selector:
    tem: apache
```