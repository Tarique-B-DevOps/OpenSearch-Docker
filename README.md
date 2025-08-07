This repository provides Docker Compose configurations for deploying OpenSearch and OpenSearch Dashboards using containerized environments. It supports both a **multi-node cluster setup** for simulating production environments and a **single-node setup** for development or testing purposes.

## Repository Contents

- `docker-compose.yml` – Defines a multi-node OpenSearch cluster with separate nodes for coordination, data, and dashboards.
- `docker-compose-dev.yml` – Defines a minimal single-node OpenSearch setup with OpenSearch Dashboards for quick testing or development.
- `.env` – Stores shared environment variables such as the initial admin password.

---

## Getting Started

### 1. Configure environment variables

Edit the `.env` file and define:

```env
OPENSEARCH_INITIAL_ADMIN_PASSWORD=your_secure_password
```

This password will be used for the `admin` user in both OpenSearch and OpenSearch Dashboards.

> You can also set environment variables directly in your shell using the `export` command:

---

### 2. Launching OpenSearch

#### To run the **multi-node cluster** in detached mode:

```sh
docker compose -f docker-compose.yml up -d
```

#### To run the **single-node development setup** in detached mode:

```sh
docker compose -f docker-compose-dev.yml up -d
```

After starting, check running containers:

```sh
docker ps
```

Monitor logs to wait for initialization to complete:

```sh
docker compose logs -f
```

---

## Testing the Deployment

Once containers are running and initialization is complete, you can verify that OpenSearch is operational using the following commands:

> **Note:** All example commands use `localhost`. If you are accessing OpenSearch from another machine, replace `localhost` with your server’s IP address.

### Check cluster health

```sh
curl -u admin:$OPENSEARCH_INITIAL_ADMIN_PASSWORD https://localhost:9200/_cluster/health?pretty -k
```

### List all nodes in the cluster

```sh
curl -u admin:$OPENSEARCH_INITIAL_ADMIN_PASSWORD https://localhost:9200/_cat/nodes?v -k
```

### View all indices

```sh
curl -u admin:$OPENSEARCH_INITIAL_ADMIN_PASSWORD https://localhost:9200/_cat/indices?v -k
```

### Search a sample index (if present)

```sh
curl -u admin:$OPENSEARCH_INITIAL_ADMIN_PASSWORD https://localhost:9200/sample-index/_search?pretty -k
```

---

## Access OpenSearch Dashboards

Once running, OpenSearch Dashboards will be available at:

```
https://localhost:5601
```

Use the same credentials (`admin` / the password from `.env`) to log in.

---

## Notes

- TLS is enabled by default; use `-k` with curl to skip certificate verification.
- Security (auth, roles, etc.) is managed via the OpenSearch security plugin.
- You can customize node settings and cluster topology in the Compose files as needed.

---