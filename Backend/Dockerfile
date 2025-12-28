# ---------- Build stage ----------
FROM rust:1.86 AS builder

WORKDIR /app

# Needed to compile postgres-related crates (Diesel/pg)
RUN apt-get update && apt-get install -y libpq-dev pkg-config && rm -rf /var/lib/apt/lists/*

# Copy manifests first for better caching
COPY Cargo.toml Cargo.lock ./

# Copy source before pre-fetch so Cargo can see targets
COPY src ./src
COPY migrations ./migrations

# Pre-fetch deps without modifying Cargo.lock (works now that src is present)
RUN cargo fetch --locked

# ensure `time` is new enough for the Rust toolchain (temporary fix)
RUN cargo update -p time --precise 0.3.36

# ✅ Install Diesel CLI in builder (creates /usr/local/cargo/bin/diesel)
RUN cargo install diesel_cli --no-default-features --features postgres

# Build release binary
RUN cargo build --release

# ---------- Runtime stage ----------
FROM debian:bookworm-slim

# Runtime libs: libpq5 needed for postgres; libpq-dev is needed only if you want diesel to work fully
# ✅ Add libpq-dev + pkg-config so diesel can load pg properly (safe choice)
RUN apt-get update && apt-get install -y libpq5 libpq-dev pkg-config ca-certificates && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the compiled binary
COPY --from=builder /app/target/release/login-app-backend /app/login-app-backend

# ✅ Copy diesel binary into runtime
COPY --from=builder /usr/local/cargo/bin/diesel /usr/local/bin/diesel

# ✅ (Optional but useful) Copy diesel.toml if you have it
# COPY diesel.toml /app/diesel.toml

EXPOSE 8080

CMD ["/app/login-app-backend"]
