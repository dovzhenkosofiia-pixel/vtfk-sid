# Етап збірки — БАГАТОЕТАПНИЙ з правильним порядком для кешу
FROM rust:1.99 AS build
WORKDIR /app

# 1. Спочатку — тільки маніфести залежностей (рідко змінюються)
COPY Cargo.toml Cargo.lock ./

# 2. Компіляція залежностей окремим шаром (заглушка main.rs)
RUN mkdir src && echo "fn main() {}" > src/main.rs \
    && cargo build --release \
    && rm -rf src

# 3. Тільки тепер — решта коду (часто змінюється)
COPY src ./src

# 4. Компіляція основного бінарника
RUN cargo build --release

# Фінальний етап — мінімальний
FROM debian:12-slim
COPY --from=build /app/target/release/sid-proj /usr/local/bin/sid-proj
CMD ["sid-proj"]