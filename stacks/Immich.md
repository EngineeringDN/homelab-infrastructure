```yaml
version: "3.9"

name: immich

networks:
  containerNetwork:
    external: true
    name: containerNetwork
  immich-internal:
    driver: bridge

services:
  # Public-facing web/API. Gets a LAN IP via macvlan.
  immich-server:
    image: ghcr.io/immich-app/immich-server:latest
    depends_on:
      - database
      - redis
      - immich-machine-learning
    environment:
      - TZ=Europe/Berlin
      - DB_DATABASE_NAME=immich
      - DB_USERNAME=postgres
      - DB_PASSWORD=<DB_PASSWORD>
      - DB_VECTOR_EXTENSION=vectorchord
      - IMMICH_HOST=0.0.0.0
      - IMMICH_PORT=2283
    volumes:
      # Main library where Immich writes uploads/imports
      - /data/immich/library:/usr/src/app/upload
    devices:
      - /dev/dri:/dev/dri
    networks:
      containerNetwork:
        ipv4_address: <IMMICH_IP>
      immich-internal: {}
    restart: unless-stopped

  # Machine Learning (faces, smart search). Internal only.
  immich-machine-learning:
    image: ghcr.io/immich-app/immich-machine-learning:latest
    environment:
      - TZ=Europe/Berlin
    volumes:
      - /compose/appdata/immich/ml-cache:/cache
    networks:
      immich-internal: {}
    restart: unless-stopped

  # Redis cache/broker. Internal only.
  redis:
    image: docker.io/redis:7-alpine
    command: ["redis-server", "--save", "60", "1", "--loglevel", "warning"]
    networks:
      immich-internal: {}
    restart: unless-stopped

  # Postgres with VectorChord + pgvector extensions (maintained by Immich).
  database:
    image: ghcr.io/immich-app/postgres:16-vectorchord0.3.0-pgvectors0.2.0
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=<DB_PASSWORD>
      - POSTGRES_DB=immich
      - TZ=Europe/Berlin
    volumes:
      - /compose/appdata/immich/postgres:/var/lib/postgresql/data
    networks:
      immich-internal: {}
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres || exit 1"]
      interval: 10s
      timeout: 5s
      retries: 10
    restart: unless-stopped

```