```yaml
version: "3.9"

networks:
  containerNetwork:
    external: true
    name: containerNetwork

services:
  mariadb:
    image: lscr.io/linuxserver/mariadb:11.4.5
    container_name: booklore-mariadb
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Berlin
      - MYSQL_ROOT_PASSWORD=<MYSQL_ROOT_PASSWORD>
      - MYSQL_DATABASE=booklore
      - MYSQL_USER=booklore
      - MYSQL_PASSWORD=<DB_PASSWORD>
    volumes:
      - /compose/appdata/booklore-mariadb:/config
    networks:
      containerNetwork:
        ipv4_address: <DB_IP>
    healthcheck:
      test: ["CMD", "mariadb-admin", "ping", "-h", "localhost"]
      interval: 5s
      timeout: 5s
      retries: 10
    restart: unless-stopped

  booklore:
    image: booklore/booklore:latest
    # alt: ghcr.io/booklore-app/booklore:latest
    container_name: booklore
    depends_on:
      mariadb:
        condition: service_healthy
    environment:
      - USER_ID=1000
      - GROUP_ID=1000
      - TZ=Europe/Berlin
      - DATABASE_URL=jdbc:mariadb://mariadb:3306/booklore
      - DATABASE_USERNAME=booklore
      - DATABASE_PASSWORD=<DB_PASSWORD>
      - BOOKLORE_PORT=6060
      - SWAGGER_ENABLED=false
    volumes:
      - /compose/appdata/booklore:/app/data
      - /data/booklore/media/books:/books
      - /data/booklore/media/bookdrop:/bookdrop
    networks:
      containerNetwork:
        ipv4_address: <IP>
    restart: unless-stopped

```