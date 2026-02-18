```yaml
version: "3.9"
networks:
  containerNetwork:
    external: true
    name: containerNetwork

services:
  db:
    image: mariadb:lts
    container_name: firefly-db
    environment:
      - MYSQL_DATABASE=firefly
      - MYSQL_USER=firefly
      - MYSQL_PASSWORD= <DB_PASSWORD>
      - MYSQL_RANDOM_ROOT_PASSWORD=yes
      - TZ=Europe/Berlin
    volumes:
      - /compose/appdata/firefly/firefly-db:/var/lib/mysql
    networks:
      containerNetwork:
        ipv4_address: <DB_IP>
    restart: unless-stopped

  firefly:
    image: fireflyiii/core:latest
    container_name: firefly
    environment:
      - APP_KEY=gX9BxbigDI0EfqFdvCxQwbMp5T7YZP62
      - APP_URL=<IP>
      - TZ=Europe/Berlin
      - DB_CONNECTION=mysql
      - DB_HOST=<DB_IP>
      - DB_PORT=3306
      - DB_DATABASE=firefly
      - DB_USERNAME=firefly
      - DB_PASSWORD=<DB_PASSWORD>

      - TRUSTED_PROXIES=192.168.0.0/24,10.0.0.0/8,127.0.0.1
      - TRUSTED_HOSTS=<LOCAL_DOMAIN>
      - FORCE_HTTPS=true
      - SESSION_SECURE_COOKIE=true
    volumes:
      - /compose/appdata/firefly/uploads:/var/www/html/storage/upload
    networks:
      containerNetwork:
        ipv4_address: <IP>
    restart: unless-stopped

  importer:
    image: fireflyiii/data-importer:latest
    container_name: firefly-importer
    environment:
      - TZ=Europe/Berlin
      # set these after Firefly is up, or via the Importer UI
      # - FIREFLY_III_URL=http://192.168.0.115
      # - FIREFLY_III_ACCESS_TOKEN=xxxxx
    networks:
      containerNetwork:
        ipv4_address: <IP>
    restart: unless-stopped
```