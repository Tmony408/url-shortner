# URL Shortener (Go + Gin + Redis)

A simple, fast URL shortener written in Go. It exposes an HTTP API to create short links and redirects users to the original URL. Backed by Redis for fast lookups, and Base58-encoded deterministic IDs.

## Features
- Generate short, Base58 IDs (fixed 8 chars)
- In-memory cache powered by Redis
- Simple HTTP API with [gin-gonic/gin]
- Unit tests for core packages (`shortener`, `store`)

## Tech Stack
- **Language:** Go (1.24+)
- **Framework:** gin-gonic/gin
- **Cache/Store:** Redis (via go-redis v8)
- **Hashing:** SHA-256 → Base58

## Getting Started

### Prerequisites
- Go 1.24+ installed
- Redis running locally

Run Redis quickly via Docker:
```bash
docker run -p 6379:6379 --name urlshort-redis -d redis:7-alpine
```

### Install & Run
Clone your repo (replace with your GitHub origin if different):
```bash
git clone https://github.com/Tmony408/url-shortner.git
cd url-shortner
```

Install dependencies:
```bash
go mod tidy
```

Run the service:
```bash
go run main.go
```
Server starts on **http://localhost:9808**.

## API

### Health / Welcome
**GET /** → 200 OK
```json
{ "message": "Welcome to the URL Shortener API" }
```

### Create Short URL
**POST /create-short-url**  
Body:
```json
{
  "long_url": "https://example.com/some/very/long/path?with=params",
  "user_id": "user-123"
}
```
Response:
```json
{
  "message": "short url created successfully",
  "short_url": "http://localhost:9808/Ab12CdEf"
}
```

### Redirect
**GET /:shortUrl**  
Redirects (302) to the original `long_url`.

Example:
```bash
curl -i http://localhost:9808/Ab12CdEf
# HTTP/1.1 302 Found
# Location: https://example.com/some/very/long/path?with=params
```

## Testing
Run all tests:
```bash
go test ./...
```

## Configuration
`store/InitializeStore()` connects to Redis on default `localhost:6379`.  
If you want to customize Redis address or credentials, extend `InitializeStore()` to read from environment variables, e.g.:
- `REDIS_ADDR` (default `localhost:6379`)
- `REDIS_DB` (default `0`)
- `REDIS_PASSWORD` (default empty)

## Roadmap / Ideas
- Rate limiting
- Expiring links / custom TTL per link
- Custom aliases (vanity URLs)
- Click analytics (count, referrer, geo, user agent)
- Dockerfile + docker-compose for one-command spin-up
- GitHub Actions CI (test/build)

