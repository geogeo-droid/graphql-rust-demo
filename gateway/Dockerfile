FROM rust:1.76.0 as builder

ENV CARGO_TERM_COLOR always
RUN apt-get update && apt-get install -y protobuf-compiler cmake

WORKDIR /usr/src/docker-build
# create empty project for caching dependencies
RUN USER=root cargo init
COPY common-utils ../common-utils
COPY Cargo.lock gateway/Cargo.toml ./
# cache dependencies
RUN cargo install --path . --locked

COPY gateway/ ./
RUN touch src/main.rs
RUN cargo install --path . --locked

FROM debian:bookworm-slim
RUN apt-get update && apt-get install -y ca-certificates curl dnsutils iputils-ping
RUN curl -sSL https://rover.apollo.dev/nix/latest | sh
RUN update-ca-certificates
COPY --from=builder /usr/local/cargo/bin/gateway /bin/
CMD ["gateway"]
