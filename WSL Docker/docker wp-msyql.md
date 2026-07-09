```sh
docker run -itd -p 3306:3306 -v sql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=It12345! -e MYSQL_DATABASE=word -e MYSQL_USER=shkim -e MYSQL_PASSWORD=It1234@ --name m1 mysql:8.0

docker exec m1 mysql -uroot -pIt12345! -e "GRANT ALL PRIVILEGES ON *.* TO 'shkim'@'%'; FLUSH PRIVILEGES;"

docker run -itd -p 65080:80 -e WORDPRESS_DB_HOST=10.0.0.11 -e WORDPRESS_DB_USER=shkim -e WORDPRESS_DB_PASSWORD=It1234@ -e WORDPRESS_DB_NAME=word --name w1 wordpress

docker cp index1.html h1:/usr/local/apache2/htdocs/index.html

mysql -uroot -pIt12345! -h172.17.0.4

```