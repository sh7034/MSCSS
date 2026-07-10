### emptyDir
파드 종속적 임시 저장소
파드와 데이터의 라이프사이클이 동일
###### `apache.yml`
```yml
apiVersion: v1
kind: Pod
metadata:
  name: apache
  labels:
    app: httpd
    env: devel
    stor: ssd
spec:
  containers:
  - name: a1
    image: httpd
    imagePullPolicy: IfNotPresent
    ports:
    - containerPort: 80
    volumeMounts:
    - mountPath: /usr/local/apache2/htdocs/
      name: shkim-vol1
  volumes:
  - name: shkim-vol1
    emptyDir: {}
```
###### `apache` 파드가 생성된 노드에서:
```sh
# 마운트 디렉터리 찾기
find / -name shkim-vol1
/var/lib/kubelet/pods/d0a609a1-74e5-4f64-aed9-2a809fa55917/volumes/kubernetes.io~empty-dir/shkim-vol1
/var/lib/kubelet/pods/d0a609a1-74e5-4f64-aed9-2a809fa55917/plugins/kubernetes.io~empty-dir/shkim-vol1
# index.html 파일 생성
cat > /var/lib/kubelet/pods/d0a609a1-74e5-4f64-aed9-2a809fa55917/volumes/kubernetes.io~empty-dir/shkim-vol1/index.html << END
<html><body><h1>K8S-emptyDir-SHKIM-TEST</h></body></html>
END
```
- 컨트롤 플레인에서
```sh
  kubectl get pod apache -o wide
  
  curl 192.168.166.162
```
  
  
### hostPath
노드 종속적 전용 보관소
[[Docker]]의 바인드마운트와 유사
##### 예제 1: `index.html` 공유
nginx 파드 n1을 먼저 생성하고 동일한 index.html 파일을 httpd 파드 h1이 공유하도록 hostpath 사용
###### `n1.yml`
```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  nodeName: node1
  containers:
  - name: n1
    image: nginx
    imagePullPolicy: Never
    ports:
    - containerPort: 80
    volumeMounts:
    - mountPath: /usr/share/nginx/html/
      name: shkim-vol
  volumes:
  - name: shkim-vol
    hostPath:
      path: /html
      type: DirectoryOrCreate
```
###### `h1.yml`
```yml
apiVersion: v1
kind: Pod
metadata:
  name: httpd
  labels:
    app: apache
spec:
  nodeSelector:
    stor: hdd
  containers:
  - name: h1
    image: httpd
    imagePullPolicy: Never
    ports:
    - containerPort: 80
    volumeMounts:
    - mountPath: /usr/local/apache2/htdocs
      name: shkim-vol1
    - mountPath: /usr/loacl/apache2/htdocs/index.html
      name: shkim-file1
  volumes:
  - name: shkim-vol1
    hostPath:
      path: /html
      type: Directory
  - name: shkim-file1
    hostPath:
      path: /html/index.html
      type: File
```
##### 예제 2: MySQL 환경변수 공유
mysql:8.0 파드 m1을 환경변수들을 포함하여 생성한 후, m2을 생성하여 같은 환경변수 파일들을 가져다 사용하도록 hostPath 사용
- 마운트 경로: `node2:/mysql -> m1:/var/lib/mysql/`
###### `m1.yml`
```yml
apiVersion: v1
kind: Pod
metadata:
  name: mysql1
  labels:
    app: mysql
spec:
  nodeSelector:
    stor: hdd
  containers:
  - name: m1
    image: mysql:8.0
    imagePullPolicy: Never
    env:
    - name: MYSQL_ROOT_PASSWORD
      value: 'It12345!'
    - name: MYSQL_DATABASE
      value: 'word'
    - name: MYSQL_USER
      value: 'shkim'
    - name: MYSQL_PASSWORD
      value: 'It12345!'
    ports:
    - containerPort: 3306
    volumeMounts:
    - mountPath: /var/lib/mysql
      name: shkim-vol1
  volumes:
  - name: shkim-vol1
    hostPath:
      path: /mysql
      type: DirectoryOrCreate
```
###### `m2.yml`
```yml
apiVersion: v1
kind: Pod
metadata:
  name: mysql2
  labels:
    app: mysql
spec:
  nodeSelector:
    stor: hdd
  containers:
  - name: m2
    image: mysql:8.0
    imagePullPolicy: Never
    ports:
    - containerPort: 3306
    volumeMounts:
    - mountPath: /var/lib/mysql
      name: shkim-vol1
  volumes:
  - name: shkim-vol1
    hostPath:
      path: /mysql
      type: Directory
```
`mysql -uroot -pIt12345! -h<mysql2 파드의 IP>`로 접속 시도하면 접속 거부됨.
`kubectl delete pods mysql1`을 사용해 첫 번째 파드를 정지 또는 삭제해야 접속 성공
##### 예제 3: 
### PersistentVolume
노드와 독립된 영구 저장소 자원
파드 이동성, 클라우드 연동 등에서 장점
- PV: 스토리지
- PVC: 스토리지 요청서
##### 예제
###### `pv.yml`
```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: shkim-pv
spec:
  storageClassName: shkim
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/web"
```
###### `pvc.yml`
```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: shkim-pvc
spec:
  storageClassName: shkim
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```
###### `pv2.yml`
```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: shkim-pv
spec:
  storageClassName: shkim-stor
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  nfs:
    server: 10.0.0.11
    path: /nfs-server
```
###### `pvc2.yml`
```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: shkim-pvc
spec:
  storageClassName: shkim-stor
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```
###### `apa.yml`
```yml
apiVersion: v1
kind: Pod
metadata:
  name: httpd
  labels:
    app: apache
spec:
  containers:
  - name: h1
    image: httpd
    imagePullPolicy: Never
    ports:
    - containerPort: 80
    volumeMounts:
    - mountPath: /usr/local/apache2/htdocs
      name: shkim-vol
  volumes:
  - name: shkim-vol
    persistentVolumeClaim:
      claimName: shkim-pvc
```
###### `ngi.yml`
```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
    env: devel
spec:
  containers:
  - name: n1
    image: nginx
    imagePullPolicy: IfNotPresent
    ports:
    - containerPort: 80
    volumeMounts:
    - mountPath: /usr/share/nginx/html
      name: shkim-stor
  volumes:
  - name: shkim-stor
    persistentVolumeClaim:
      claimName: shkim-pvc

```

