### 컨테이너 오케스트레이터
- 선언적 구성 (Declarative Configuration)
- 자가 치유
- 자동 스케일링
- 로드 밸런싱
```sh
# docker에서 이미지를 가져와 임포트
docker pull nginx:1.14
docker save -o nginx.tar nginx:1.14
scp nginx.tar root@10.0.0.12:/root
# docker 이미지를 임포트
ctr -n k8s.io image import nginx.tar

# yaml 배포
kubectl apply -f myword.yml
# 테스트 배포
kubectl apply -f nginx.yml --dry-run=server

kubectl get po -o wide
# 2초 간격 모니터링
watch -n 2 kubectl get pod,rs,deploy -o wide

# 파드 내 명령실행
kubectl exec -it dep-nginx-555fbbdd6f-6gmh7 -- nginx -v
#디플로이먼트 설정 수정
kubectl edit deploy dep-nginx
```
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

# 노드에 레이블 부여
kubectl label nodes <노드이름> <labelKey>=<Value>
kubectl get nodes --show-labels
```

- .yml 파일에서 파드가 생성될 노드 지정 방법 2가지:
```yml
spec:
  nodeName: <노드이름>
```

```yml
spec:
  nodeSelector:
    <labelKey>: <Value>
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
# 명령 실행
kubectl exec <파드이름> -- <명령어>

# cordon: 노드에 신규 파드 스케줄링 금지
kubectl cordon <노드이름>
# uncordon: cordon 해제
kubectl uncordon <노드이름>
# drain: 노드에 신규 파드 스케줄링 금지 후 이미 생성된 파드를 다른 노드로 이전(evict)
kubectl drain <노드이름> --ignore-daemonsets --delete-emptydir-data
```
###### `nginx.yml`
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
#### Service
동적으로 변하는 파드들을 외부와 연결해 주는 고정된 통로(접근점)
파드의 집합에 접근할 수 있는 고정된 IP와 포트를 제공하는 객체
파드는 생성되고 삭제될 때마다 내부 IP가 계속 바뀌는데, 서비스는 이 가변적인 파드들 앞단에 위치하여, IP가 바뀌어도 클라이언트가 항상 동일한 주소로 접속할 수 있도록 트래픽을 유도
```sh
kubectl get svc
kubectl expose --name nginx deploy dep-nginx --type=NodePort
```

##### ClusterIP 
클러스터 내부에서만 접근할 수 있는 가상의 고정 IP 부여
외부 통신 불가, 같은 클러스터 내의 파드들끼리 통신하는 용도
서비스의 기본 설정값
- 포트 키: 
	- `port`: 서비스 오브젝트가 사용하는 포트
	- `targetPort`=`containerPort`: 파드가 열어둔 포트. 전자는 서비스를 선언할 때, 후자는 파드나 디플로이먼트를 선언할 때 쓰는 키 이름
###### clusterNgPo.yml
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
##### NodePort
모든 노드 컴퓨터의 포트 30000-32767 중 똑같은 번호 하나를 열어 외부와 통신
외부에서 `<NodeIP>:<NodePort>`로 접속하면 해당 서비스가 지정하는 파드로 트래픽을 전달
- 포트 키:
	- `nodePort`: 외부 사용자가 노드의 IP로 접속할 때 사용하는 포트
###### nodeNgPo.yml
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
#### Namespace
단일 클러스터의 논리적 분할
가상 클러스터
RBAC 적용 가능
```sh
kubectl get namespaces
kubectl delete ns <네임스페이스>
# 오브젝트를 특정 네임스페이스에 생성
kubectl apply -f ingress.yml --namespace ingress-nginx
```
###### `mysqlword.yml`
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
###### `httpd-dep.yml`
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
###### `mysql-dep.yml`
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
###### `wordpress-dep.yml`
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

