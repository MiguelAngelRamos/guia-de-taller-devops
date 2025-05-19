```yaml
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    container_name: mysql
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: academy
      MYSQL_DATABASE: laravel
      MYSQL_USER: laravel
      MYSQL_PASSWORD: academy
    volumes:
      - /home/miguel/mysql-compose/mysql_data:/var/lib/mysql
    ports:
      - "3306:3306"
    networks:
      - laravel

volumes:
  mysql_data:

networks:
  laravel:
    driver: bridge

```