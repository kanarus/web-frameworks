FROM rust:1.89-slim-bookworm AS build

WORKDIR /usr/src/app

{{#files}}
  COPY '{{source}}' '{{target}}'
{{/files}}

RUN apt update
RUN apt install -y --no-install-recommends git pkg-config curl
RUN rm -rf /var/lib/apt/lists/*

ENV RUSTFLAGS='-C target-cpu=native'
RUN cargo build --release  \
  --config  'profile.release.lto=true' \
  --config  'profile.release.panic="abort"' \
  --config  'profile.release.codegen-units=1'

### DEBUG ###
RUN timeout -sKILL 5s ./target/release/server; echo "[timeout] exit code: $?"
#############

# FROM debian:stable-slim
FROM gcr.io/distroless/cc-debian12

COPY --from=build /usr/bin/curl /usr/bin/curl
COPY --from=build /usr/src/app/target/release/server /app/server

# RUN apt update
# RUN apt install -y --no-install-recommends curl

### DEBUG ###
RUN uname -r
RUN {{command}}
#############

# ENTRYPOINT {{command}}

HEALTHCHECK CMD curl --fail http://0.0.0.0:3000 || exit 1
