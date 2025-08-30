FROM rust:1.89-slim-bookworm AS build

WORKDIR /usr/src/app

{{#files}}
  COPY '{{source}}' '{{target}}'
{{/files}}

RUN apt-get -qq update
RUN apt-get -qy install git pkg-config

RUN cargo build --release  \
  --config  'profile.release.lto=true' \
  --config  'profile.release.panic="abort"' \
  --config  'profile.release.codegen-units=1'

### DEBUG ###
RUN timeout -sKILL 5s ./target/release/server; echo "[timeout] exit code: $?"
#############

FROM debian:stable-slim

COPY --from=build /usr/src/app/target/release/server /usr/src/app/target/release/server

RUN apt-get -qq update
RUN apt-get -qy install curl

### DEBUG ###
RUN uname -r
RUN {{command}}
#############

# ENTRYPOINT {{command}}

HEALTHCHECK CMD curl --fail http://0.0.0.0:3000 || exit 1
