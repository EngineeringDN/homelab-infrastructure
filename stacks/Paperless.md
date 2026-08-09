```yaml
version: "3.9"

networks:
  containerNetwork:
    external: true
    name: containerNetwork
  paperless:
    driver: bridge

services:
  # Web app, attached to LAN and the internal backend network
  paperless:
    image: ghcr.io/paperless-ngx/paperless-ngx:latest
    container_name: paperless
    depends_on:
      - db
      - redis
      - gotenberg
      - tika
    environment:
      # Required services
      PAPERLESS_REDIS: redis://redis:6379
      PAPERLESS_DBHOST: paperless-db
      PAPERLESS_DBPORT: 5432
      PAPERLESS_DBNAME: paperless
      PAPERLESS_DBUSER: paperless
      PAPERLESS_DBPASS: <DB_PASSWORD>
      
      # Optional services for Office docs and EML parsing
      PAPERLESS_TIKA_ENABLED: "true"
      PAPERLESS_TIKA_ENDPOINT: http://tika:9998
      PAPERLESS_TIKA_GOTENBERG_ENDPOINT: http://gotenberg:3000

      # Locale and OCR
      PAPERLESS_TIME_ZONE: Europe/Berlin
      PAPERLESS_OCR_LANGUAGE: deu
      # Install extra OCR languages on first run, e.g. "deu eng"
      PAPERLESS_OCR_LANGUAGES: deu eng

      # Security and bootstrap
      PAPERLESS_SECRET_KEY: <LONGKEY>
      PAPERLESS_ADMIN_USER: <ADMIN>
      PAPERLESS_ADMIN_PASSWORD: <ADMIN_PASSWORD>
      PAPERLESS_ADMIN_MAIL: <EMAIL>

      # Map container user to host user for file ownership
      USERMAP_UID: "1000"
      USERMAP_GID: "1000"

    volumes:
      - /compose/appdata/paperless/data:/usr/src/paperless/data
      - /compose/appdata/paperless/media:/usr/src/paperless/media
      - /data/paperless/export:/usr/src/paperless/export
      - /data/paperless/consume:/usr/src/paperless/consume

    networks:
      containerNetwork:
        ipv4_address: <IP>
      paperless:

    restart: unless-stopped

  # PostgreSQL database
  db:
    image: postgres:16-alpine
    container_name: paperless-db
    environment:
      POSTGRES_DB: paperless
      POSTGRES_USER: paperless
      POSTGRES_PASSWORD: <DB_PASSWORD>
    volumes:
      - /compose/appdata/paperless/postgres:/var/lib/postgresql/data
    networks: [paperless]
    restart: unless-stopped

  # Redis broker/cache
  redis:
    image: redis:7-alpine
    container_name: paperless-redis
    command: ["redis-server", "--appendonly", "yes"]
    volumes:
      - /compose/appdata/paperless/redis:/data
    networks: [paperless]
    restart: unless-stopped

  # Office conversions to PDF
  gotenberg:
    image: gotenberg/gotenberg:8
    container_name: paperless-gotenberg
    networks: [paperless]
    restart: unless-stopped

  # Content parsing
  tika:
    image: docker.io/apache/tika:latest
    container_name: paperless-tika
    networks: [paperless]
    restart: unless-stopped

```