# Setup n8n with Docker locally

Create an installation folder:

```
mkdir n8n-docker
cd n8n-docker
```

Create a docker-compose.yml file with contents:

```
services:
  n8n:
    image: n8nio/n8n
    restart: always
    ports:
      - "5678:5678"
    environment:
      - GENERIC_TIMEZONE=Europe/Athens
    volumes:
      - ./n8n_data:/home/node/.n8n
volumes:
  n8n_data:
```

Create Docker container `docker compose up -d`. Give permission to n8n to write at the n8n_data folder `sudo chown -R 1000:1000 ./n8n_data` and start Docker container `docker start [container name]`. Visit http://localhost:5678/setup to start n8n.


