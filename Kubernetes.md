### 컨테이너 오케스트레이터
- 선언적 구성 (Declarative Configuration)
- 자가 치유
- 자동 스케일링
- 로드 밸런싱

#### Cluster
전체 시스템
마스터(컨트롤 플레인)와 노드 컴퓨터들로 구성

```sh
kubeadm token list
kubeadm create token
sudo kubeadm join 10.0.0.11:6443 --token 98pkia... --discovery-token-ca-cert-hash sha256:bba135...
```
#### Node
애플리케이션이 실제로 실행되는 물리적 공간(서버 컴퓨터)
클러스터를 구성하는 개별 서버
컨테이너가 구동될 수 있도록 CPU와 메모리 리소스 제공
```sh
kubectl get nodes
```
#### Pod
쿠버네티스가 인식하고 관리하는 **가장 작은 실행 단위**
하나 또는 그 이상의 컨테이너를 감싸고 있는 논리적인 단일 상자
쿠버네티스는 컨테이너를 개별적으로 하나씩 제어하지 않고, 반드시 '파드'라는 단위로 묶어서 노드에 배치하고 실행
```sh
kubectl get pods
# alpine 이미지로 만들고 sh 실행
kubectl run alpine -it --image=alpine -- sh
# dry-run 으로 실제 실행하지는 않고 체크 가능
kubectl run httpd --image=httpd --dry-run=client -o yaml > httpd.yml
# describe
kubectl describe po <파드이름>
```
#### Service
동적으로 변하는 파드들을 외부와 연결해 주는 고정된 통로(접근점)
파드의 집합에 접근할 수 있는 고정된 IP와 포트를 제공하는 객체
파드는 생성되고 삭제될 때마다 내부 IP가 계속 바뀌는데, 서비스는 이 가변적인 파드들 앞단에 위치하여, IP가 바뀌어도 클라이언트가 항상 동일한 주소로 접속할 수 있도록 트래픽을 유도
```sh
kubectl get svc
kubectl expose --name nginx deploy dep-nginx --type=NodePort
```
#### Namespace
단일 클러스터의 논리적 분할
가상 클러스터
RBAC 적용 가능
```sh
kubectl get namespaces
kubectl delete ns <네임스페이스>
```

```sh
# yaml 적용
kubectl apply -f myword.yml
kubectl apply -f nginx.yml --dry-run=server

kubectl get po -o wide
# 2초 간격 모니터링
watch -n 2 kubectl get pod,rs,deploy -o wide
```
###### mysql + wordpress 복합 pod 코드
```yml
apiVersion: v1
kind: Pod
metadata:
  name: myword
  labels:
    app: sql
    env: prod
spec:
  containers:
    - name: m1
      image: mysql:8.0
      imagePullPolicy: IfNotPresent
      ports:
        - containerPort: 3306
      env:
        - name: MYSQL_ROOT_PASSWORD
          value: 'It12345!'
        - name: MYSQL_DATABASE
          value: 'word'
        - name: MYSQL_USER
          value: 'shkim'
        - name: MYSQL_PASSWORD
          value: 'It12345!'
    - name: w1
      image: wordpress
      imagePullPolicy: IfNotPresent
      ports:
        - containerPort: 80
      env:
        - name: WORDPRESS_DB_HOST
          value: 'myword'
        - name: WORDPRESS_DB_NAME
          value: 'word'
        - name: WORDPRESS_DB_USER
          value: 'shkim'
        - name: WORDPRESS_DB_PASSWORD
          value: 'It12345!'
```

#### ReplicaSet
지정된 수의 파드가 항상 실행되도록 보장하는 다중화 컨트롤러
외부에 노출 불가
```sh
kubectl scale --replicas=3 rs/rep-nginx
kubectl scale --replicas=2 -f nginx.yml
kubectl apply -f nginx.yml
kubectl get pod,rs
```

#### Deployment
레플리카셋을 상위에서 추상화하여, 애플리케이션의 배포 및 버전 관리(롤링 업데이트, 롤백)를 자동화하는 가장 대중적인 워크로드
- 배포 전략
	- Rolling Update: 기존 버전 파드를 하나씩 죽이고 새 버전 파드를 하나씩 띄움
	- Blue-Green: 완전히 새로운 버전을 따로 띄워 트래픽을 전환
###### httpd 디플로이먼트 코드
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dep-httpd
  labels:
    app: httpd
    env: prod
spec:
  replicas: 5
  selector:
    matchLabels:
      tem: httpd
  template:
    metadata:
      name: tem-httpd
      labels:
        tem: httpd
    spec:
      containers:
        - name: h1
          image: httpd
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 80
          resource:
            limits:
              cpu: "200m"
              memory: "100Mi"
            requests:  
              cpu: "100m"
              memory: "50Mi"

```
###### mysql 디플로이먼트 코드
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dep-mysql
  labels:
    app: mysql
    env: prod
spec:
  replicas: 1
  selector:
    matchLabels:
      tem: mysql
  template:
    metadata:
      name: tem-mysqll
      labels:
        tem: mysql
    spec:
      containers:
        - name: m1
          image: mysql:8.0
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: 'It12345!'
            - name: MYSQL_DATABASE
              value: 'word'
            - name: MYSQL_USER
              value: 'shkim'
            - name: MYSQL_PASSWORD
              value: 'It12345!'
```
###### wordpress 디플로이먼트 코드
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dep-word
  labels:
    app: wordpress
    env: prod
spec:
  replicas: 3
  selector:
    matchLabels:
      tem: word
  template:
    metadata:
      name: tem-word
      labels:
        tem: word
    spec:
     containers:
        - name: w1
          image: wordpress
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 80
          env:
            - name: WORDPRESS_DB_HOST
              value: 192.168.135.14
            - name: WORDPRESS_DB_NAME
              value: 'word'
            - name: WORDPRESS_DB_USER
              value: 'shkim'
            - name: WORDPRESS_DB_PASSWORD
              value: 'It12345!'
```

###### podnginx.yml
```yml
apiVersion: v1
kind: Pod
metadata: 
  name: nginx
  labels:
    app: nginx
    env: test
spec:
  containers:
    - name: n1
      image: nginx
      imagePullPolicy: IfNotPresent
      ports:
        - containerPort: 80
```
###### clusterpo.yml
```yml
apiVersion: v1
kind: Service
metadata:
  name: svc-ngpod
  labels:
    env: test
spec:
  type: ClusterIP
  ports:
  - port: 8080
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
```

###### nodeng.yml
```yml
apiVersion: v1
kind: Service
metadata:
  name: node-nginx
  labels:
    env: test
    type: node
spec:
  type: NodePort
  ports:
  - port: 8080
    nodePort: 30000
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
```