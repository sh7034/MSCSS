여러 컨테이너로 구성된 애플리케이션을 한 번에 정의하고 실행하는 도구

### compose.yaml
- `services`: 실행할 컨테이너들
 - `ports`: 호스트 컴퓨터와 포트 연결
 - `expose`: 컨테이너 상대로 포트 개방
- `networks`: 서비스끼리 통신하는 네트워크 
- `volumes`: 데이터 영속 저장소

### 예시
```yaml
services:
  web:
    build: .
    container_name: compose-flask-web
    ports:
      - "5001:5000"
```

```yaml
services:
  flaskapp:
    build: ./flask # flask 폴더엔 Dockerfile이 존재함
    container_name: flaskapp
    expose:
      - "5000"

  nginx:
    image: nginx:latest
    container_name: nginx-front
    ports:
      - "8080:80"
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - flaskapp
```

```yaml
services:
  db:
    image: mysql:8.0
    container_name: wp-mysql
    restart: always
    environment:
      MYSQL_DATABASE: wordpressdb
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppass123
      MYSQL_ROOT_PASSWORD: rootpass123
    volumes:
      - db_data:/var/lib/mysql
    command: --default-authentication-plugin=mysql_native_password
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-prootpass123"]
      interval: 10s
      timeout: 5s
      retries: 10

  wordpress:
    image: wordpress:latest
    container_name: wp-web
    restart: always
    depends_on:
      db:
        condition: service_healthy
    ports:
      - "8081:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_NAME: wordpressdb
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppass123
    volumes:
      - wp_data:/var/www/html

volumes:
  db_data:
  wp_data:
```