##################################################### BUILD STAGE ######################################################
FROM photoprism/develop:lunar AS build

# Copyright © 2018 - 2024 PhotoPrism UG. All rights reserved.
#
# Questions? Email us at hello@photoprism.app or visit our website to learn
# more about our team, products and services: https://www.photoprism.app/

# Declare build parameters.
ARG TARGETARCH
ARG TARGETPLATFORM
ARG BUILD_TAG

# Copy source to image.
WORKDIR "/go/src/github.com/photoprism/photoprism"
COPY . .

# Build app.
RUN make all install DESTDIR=/opt/photoprism

################################################## PRODUCTION STAGE ####################################################
#### Base Image: Ubuntu 23.04 (Lunar Lobster)
FROM photoprism/develop:lunar-slim

# Add Open Container Initiative (OCI) annotations.
# See: https://github.com/opencontainers/image-spec/blob/main/annotations.md
LABEL org.opencontainers.image.title="PhotoPrism® (Ubuntu 23.04)"
LABEL org.opencontainers.image.description="Ubuntu 23.04 (Lunar Lobster)"
LABEL org.opencontainers.image.url="https://hub.docker.com/r/photoprism/photoprism"
LABEL org.opencontainers.image.source="https://github.com/photoprism/photoprism"
LABEL org.opencontainers.image.documentation="https://docs.photoprism.app/getting-started/"
LABEL org.opencontainers.image.authors="PhotoPrism UG <hello@photoprism.app>"
LABEL org.opencontainers.image.vendor="PhotoPrism UG"

# Declare build parameters.
ARG TARGETARCH
ARG BUILD_TAG

# Set environment variables, see https://docs.photoprism.app/getting-started/config-options/.
ENV PHOTOPRISM_ARCH=$TARGETARCH \
    DOCKER_TAG=$BUILD_TAG \
    DOCKER_ENV="prod" \
    TMPDIR="/tmp" \
    DEBIAN_FRONTEND="noninteractive" \
    TF_CPP_MIN_LOG_LEVEL="2" \
    MALLOC_ARENA_MAX="4" \
    PROG="photoprism" \
    PHOTOPRISM_ASSETS_PATH="/opt/photoprism/assets" \
    PHOTOPRISM_IMPORT_PATH="/photoprism/import" \
    PHOTOPRISM_ORIGINALS_PATH="/photoprism/originals" \
    PHOTOPRISM_STORAGE_PATH="/photoprism/storage" \
    PHOTOPRISM_BACKUP_PATH="/photoprism/storage/backups" \
    PHOTOPRISM_LOG_FILENAME="/photoprism/storage/photoprism.log" \
    PHOTOPRISM_PID_FILENAME="/photoprism/storage/photoprism.pid" \
    PHOTOPRISM_DEBUG="false" \
    PHOTOPRISM_PUBLIC="false" \
    PHOTOPRISM_READONLY="false" \
    PHOTOPRISM_UPLOAD_NSFW="true" \
    PHOTOPRISM_DETECT_NSFW="false" \
    PHOTOPRISM_EXPERIMENTAL="false" \
    PHOTOPRISM_SITE_URL="http://localhost:2342/" \
    PHOTOPRISM_SITE_CAPTION="AI-Powered Photos App" \
    PHOTOPRISM_SITE_DESCRIPTION="" \
    PHOTOPRISM_SITE_AUTHOR="" \
    PHOTOPRISM_HTTP_HOST="0.0.0.0" \
    PHOTOPRISM_HTTP_PORT=2342 \
    PHOTOPRISM_DATABASE_DRIVER="sqlite" \
    PHOTOPRISM_DATABASE_SERVER="" \
    PHOTOPRISM_DATABASE_NAME="photoprism" \
    PHOTOPRISM_DATABASE_USER="photoprism" \
    PHOTOPRISM_DATABASE_PASSWORD="" \
    PHOTOPRISM_DISABLE_CHOWN="false" \
    PHOTOPRISM_DISABLE_WEBDAV="false" \
    PHOTOPRISM_DISABLE_SETTINGS="false" \
    PHOTOPRISM_DISABLE_BACKUPS="false" \
    PHOTOPRISM_DISABLE_EXIFTOOL="false" \
    PHOTOPRISM_DISABLE_PLACES="false" \
    PHOTOPRISM_DISABLE_TENSORFLOW="false" \
    PHOTOPRISM_DISABLE_FACES="false" \
    PHOTOPRISM_DISABLE_CLASSIFICATION="false" \
    PHOTOPRISM_RAW_PRESETS="false" \
    PHOTOPRISM_THUMB_UNCACHED="false" \
    PHOTOPRISM_AUTO_INDEX="300" \
    PHOTOPRISM_AUTO_IMPORT="-1" \
    PHOTOPRISM_INIT="https"

# Copy scripts.
COPY --chown=root:root --chmod=755 /scripts/dist/ /scripts/

# Update pre-installed packages.
RUN apt-get update && \
    apt-get -qq dist-upgrade && \
    /scripts/cleanup.sh

# Default working directory.
WORKDIR /photoprism

# Expose HTTP(S) ports.
EXPOSE 2342 2443

# Copy app files.
COPY --from=build --chown=root:root --chmod=755 /opt/photoprism/ /opt/photoprism

# Declare container entrypoint script.
ENTRYPOINT ["/scripts/entrypoint.sh"]

# Start app.
CMD ["/opt/photoprism/bin/photoprism", "start"]
