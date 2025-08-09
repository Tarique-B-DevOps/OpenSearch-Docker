This repository provides Docker Compose configurations for deploying OpenSearch and OpenSearch Dashboards using containerized environments. It supports both a **multi-node cluster setup** for simulating production environments and a **single-node setup** for development or testing purposes.

## Repository Contents

- `docker-compose.yml` – Multi-node OpenSearch cluster.
- `docker-compose-dev.yml` – Single-node OpenSearch setup for development.
- `.env` – Environment variables for multi-node setup.
- `.env.dev` – Environment variables for single-node (dev) setup.

---

## Getting Started

### 1. Configure environment variables

Edit the `.env` file for multi-node or `.env.dev` for single-node development.

---

### 2. Spin Up OpenSearch Cluster

#### Multi-node cluster:

```sh
docker compose -f docker-compose.yml up -d
```

#### Single-node development setup:

```sh
docker compose --env-file .env.dev -f docker-compose-dev.yml up -d
```

#### Check logs and wait for initialization to complete:

```sh
docker compose logs -f
```

---

## Verify the Deployment with OpenSearch REST API.

Before running commands, export your admin password and OpenSearch host:

```sh
export OPENSEARCH_INITIAL_ADMIN_PASSWORD='Str0ng!Passw0rd@2025'
export OPENSEARCH_HOST=localhost:9200
```
> If running on a remote server, replace `localhost` with your server IP.

### 1. Check cluster health

```sh
curl -u admin:$OPENSEARCH_INITIAL_ADMIN_PASSWORD \
  https://$OPENSEARCH_HOST/_cluster/health?pretty \
  -k
```
> A healthy cluster will return a JSON response showing the status as ***green*** or ***yellow***


### 2. Create an index

```sh
curl -u admin:$OPENSEARCH_INITIAL_ADMIN_PASSWORD \
  -X PUT "https://$OPENSEARCH_HOST/alerts?pretty" \
  -k
```

### 3. Insert data

```sh
curl -u admin:$OPENSEARCH_INITIAL_ADMIN_PASSWORD \
  -X POST "https://$OPENSEARCH_HOST/alerts/_doc/1?pretty" \
  -H 'Content-Type: application/json' \
  -d '{
    "timestamp": "2025-08-08T10:23:45Z",
    "event_type": "failed_login",
    "source_ip": "198.51.100.23",
    "destination_ip": "192.168.1.10",
    "username": "admin",
    "auth_method": "password",
    "status": "failure",
    "severity": "high",
    "message": "Multiple failed login attempts detected from external IP."
  }' \
  -k

```

### 4. Retrieve the inserted document

```sh
curl -u admin:$OPENSEARCH_INITIAL_ADMIN_PASSWORD \
  "https://$OPENSEARCH_HOST/alerts/_doc/1?pretty" \
  -k
```

---

## Access OpenSearch Dashboards

Once running, OpenSearch Dashboards will be available at:

```
http://localhost:5601
```
> If running on a remote server, replace `localhost` with your server IP.

Use the credentials (`admin` / password from your environment file) to log in.

---

## Notes

- TLS is enabled by default; use `-k` with curl to skip certificate verification.
- Security (auth, roles, etc.) is managed via the OpenSearch security plugin.
- You can customize node settings and cluster topology in the Compose files as needed.