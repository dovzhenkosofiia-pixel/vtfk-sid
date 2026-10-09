# Етап збірки — БАГАТОЕТАПНИЙ з правильним порядком для кешу
FROM rust:1.99 AS build
WORKDIR /app

# 1. Додаємо musl-таргет і musl-tools (компілятор C для musl)
RUN rustup target add x86_64-unknown-linux-musl \
    && apt-get update \
    && apt-get install -y musl-tools

# 2. Спочатку — тільки маніфести залежностей
COPY Cargo.toml Cargo.lock ./

# 3. Компіляція залежностей (musl) з заглушкою main.rs
RUN mkdir src && echo "fn main() {}" > src/main.rs \
    && cargo build --release --target x86_64-unknown-linux-musl \
    && rm -rf src

# 4. Тільки тепер — решта коду
COPY src ./src

# 5. Компіляція бінарника (musl) — статично
RUN cargo build --release --target x86_64-unknown-linux-musl

# Фінальний етап — scratch (порожній, без ОС)
FROM scratch
COPY --from=build /app/target/x86_64-unknown-linux-musl/release/sid-proj /sid-proj
ENTRYPOINT ["/sid-proj"]