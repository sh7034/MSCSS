#### ConfigMap
환경변수 등 설정 정보를 코드와 분리하여 저장하는 용도
##### 예제 1: MySQL-Wordpress 연동용 환경변수 (.conf 파일 사용)
```sh
kubectl create configmap mysqlenv --from-env-file mysql.conf
kubectl create configmap wordenv --from-env-file word.conf

kubectl expose --name svcmysql pod mysql --port 3306
```
###### mysql.conf
```sh
MYSQL_ROOT_PASSWORD=It12345!
MYSQL_DATABASE=word
MYSQL_USER=shkim
MYSQL_PASSWORD=It12345!
```
###### word.conf
```sh
WORDPRESS_DB_HOST=svc-mysql
WORDPRESS_DB_NAME=word
WORDPRESS_DB_PASSWORD=It12345!
WORDPRESS_DB_USER=shkim
```

##### 예제 2: MySQL-Wordpress 연동용 환경변수 (.yml 파일 사용)
###### mysqlconf.yml
```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysqlenv
  labels:
    app: mysql
    env: devel
data:
  MYSQL_ROOT_PASSWORD: It12345!
  MYSQL_DATABASE: word
  MYSQL_USER: shkim
  MYSQL_PASSWORD: It12345!
```
###### wordconf.yml
```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: wordenv
  labels:
    app: word
    env: devel
data:
  WORDPRESS_DB_HOST: svcmysql
  WORDPRESS_DB_NAME: word
  WORDPRESS_DB_USER: shkim
  WORDPRESS_DB_PASSWORD: It12345!

```
###### mysql.yml
```yml
apiVersion: v1
kind: Pod
metadata:
  name: mysql
  labels:
    app: mysql
    env: devel
spec:
  containers:
    - name: m1
      image: mysql:8.0
      imagePullPolicy: Never
      ports:
      - containerPort: 3306
      envFrom:
      - configMapRef:
          name: mysqlenv
```
###### word.yml
```yml
apiVersion: v1
kind: Pod
metadata:
  name: wordpress
  labels:
    app: wordpress
    env: devel
spec:
  containers:
    - name: w1
      image: wordpress
      imagePullPolicy: Never
      ports:
      - containerPort: 80
      envFrom:
      - configMapRef:
          name: wordenv
```
##### 예제 3: httpd 및 nginx용 index.html 
indexdata.yml로 index라는 이름의 ConfigMap을 생성, babo.html과 coco.html을 정의
각각 Nginx 파드와 Apache2 파드에서 루트 페이지 문서로 사용케 지정
###### indexdata.yml
```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: index
data:
  babo.html: |
    <html>
    <body>
    <h1>SHKIM-CONFIGMAP-NGINX</h1>
    </body>
    </html>
  coco.html: |
    <html>
    <body>
    <h1>SHKIM-CONFIGMAP-APACHE</h1>
    </body>
    </html>
```
###### NgPo.yml
```yml
apiVersion: v1
kind: Pod
metadata:
  name: indexnginx
spec:
  containers:
  - name: n1
    image: nginx
    imagePullPolicy: Never
    ports:
    - containerPort: 80
    volumeMounts:
    - mountPath: /usr/share/nginx/html/index.html
      subPath: babo.html
      name: shkim-vol
  volumes:
  - name: shkim-vol
    configMap:
      name: index
```
###### ApPo.yml
```yml
apiVersion: v1
kind: Pod
metadata:
  name: indexhttpd
spec:
  containers:
  - name: h1
    image: httpd
    imagePullPolicy: Never
    ports:
    - containerPort: 80
    volumeMounts:
    - mountPath: /usr/local/apache2/htdocs/index.html
      subPath: coco.html
      name: shkim-vol
  volumes:
  - name: shkim-vol
    configMap:
      name: index
```

#### Secret
비밀번호, API 키, SSH 키 등의 민감정보를 난독화하여 저장
Base64 인코딩 적용
##### 예제 4: MySQL 비밀번호 환경변수만 secret으로 저장

###### mysqlconf.yml
```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysqlenv
  labels:
    app: mysql
    env: devel
data:
  MYSQL_DATABASE: word
  MYSQL_USER: shkim
```
###### mysqlsec.yml
```sh
echo -n 'It12345' | base64
SXQxMjM0NSE=
```

```yml
apiVersion: v1
kind: Secret
metadata:
  name: sqlsec
data:
  MYSQL_ROOT_PASSWORD: SXQxMjM0NQo=
  MYSQL_PASSWORD: SXQxMjM0NQo=
```


# d