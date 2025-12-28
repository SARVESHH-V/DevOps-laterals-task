#build 
FROM rust:1.86 AS builder

WORKDIR /app
RUN apt-get update && apt-get install -y libpq-dev pkg-config && rm -rf /var/lib/apt/lists/*
COPY Cargo.toml Cargo.lock ./
COPY src ./src
COPY migrations ./migrations
RUN cargo fetch --locked
RUN cargo update -p time --precise 0.3.36
RUN cargo install diesel_cli --no-default-features --features postgres
RUN cargo build --release

#Runtime
FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y libpq5 libpq-dev pkg-config ca-certificates && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY --from=builder /app/target/release/login-app-backend /app/login-app-backend
COPY --from=builder /usr/local/cargo/bin/diesel /usr/local/bin/diesel
EXPOSE 8080
CMD ["/app/login-app-backend"]
