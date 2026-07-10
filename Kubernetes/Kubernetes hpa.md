Horizontal Pod Autoscaler

```sh
# 오토스케일
kubectl autoscale deploy scldep-apache --cpu-percent=50 --min=2 --max=10
# 부하테스트
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never --image-pull-policy=IfNotPresent -- /bin/sh -c "while sleep 0.01; do wget -q -O - http://svc-apache; done"

kubectl get po,hpa
```
###### `dep-apache.yml`
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dep-apache
  labels:
    app: httpd
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
          image: registry.k8s.io/hpa-example
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 80
          resources:
            limits:
              cpu: "300m"
            requests:
              cpu: "200m"

```
###### `svc-apache.yml`
```yml
apiVersion: v1
kind: Service
metadata: 
  name: svc-apache
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    tem: apache
```

