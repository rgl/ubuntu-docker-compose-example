FROM golang:1.24.5-bookworm AS builder
WORKDIR /app
COPY go.* ./
RUN go mod download
COPY *.go ./
RUN CGO_ENABLED=0 go build -ldflags="-s"

# NB we use the bookworm-slim (instead of scratch) image so we can enter the container to execute bash etc.
FROM debian:bookworm-slim
EXPOSE 8888
WORKDIR /app
COPY --from=builder /app/hello ./
ENTRYPOINT ["/app/hello"]
