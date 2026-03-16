FROM debian:trixie-20260202-slim AS build

ARG DEBIAN_FRONTEND='noninteractive'

RUN apt-get update && apt-get install -y \
  apt-transport-https ca-certificates \
  debhelper dpkg-dev devscripts \
  build-essential

ARG TARGETPLATFORM
ARG PKG_VERSION
ARG PKG_BUILD_NUMBER

ADD sources.tar /build/ucarp-${PKG_VERSION}-${PKG_BUILD_NUMBER}.1/
RUN ls -lRt /build

WORKDIR /build/ucarp-${PKG_VERSION}-${PKG_BUILD_NUMBER}.1
RUN cat | bash <<'EOF'
  printf '%s\n' \
    "ucarp (${PKG_VERSION}-${PKG_BUILD_NUMBER}.1) unstable; urgency=medium" \
    '' \
    '  * Latest GHA Build' \
    '' \
    " -- RJ Bergeron <rj@bbxn.us>  $(date '+%a, %d %b %Y %H:%M:%S %z')" \
  > debian/changelog.new
  cat debian/changelog >> debian/changelog.new
  mv debian/changelog.new debian/changelog
EOF
RUN mk-build-deps \
  -i \
  --tool 'apt-get --option Debug::pkgProblemResolver=yes --no-install-recommends --yes -qq'

RUN dpkg-buildpackage

FROM scratch as artifact

COPY --from=build "/build/*.deb" ./
