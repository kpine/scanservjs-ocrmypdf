VERSION 0.7

ARG SCANSERVJS_VERSION=v3.0.3

FROM docker.io/sbs20/scanservjs:$SCANSERVJS_VERSION

RUN apt-get update && apt-get install -y --no-install-recommends \
      ocrmypdf \
      pngquant \
      unpaper

jbig2enc:
  RUN apt-get install -y --no-install-recommends \
        automake \
        build-essential \
        curl \
        libleptonica-dev \
        libtool \
        zlib1g-dev

  WORKDIR /jbig2
  RUN curl -L https://github.com/agl/jbig2enc/archive/ea6a40a.tar.gz \
    | tar xz --strip-components=1 \
   && ./autogen.sh \
   && ./configure --prefix=/build \
   && make \
   && make install

  SAVE ARTIFACT /build

ocrmypdf:
  RUN apt-get install -y --no-install-recommends \
        python3-venv

  ARG OCRMYPDF_VERSION=15.4.3

  RUN python3 -m venv /venv \
   && /venv/bin/pip install ocrmypdf==$OCRMYPDF_VERSION

  SAVE ARTIFACT /venv

image:
  COPY +ocrmypdf/venv /venv
  COPY +jbig2enc/build /usr/local

  ENV PATH="/venv/bin:$PATH"

  ARG EARTHLY_GIT_SHORT_HASH
  ARG BUILD_DATE=$(date -u +"%Y-%m-%dT%H:%M:%SZ")

  LABEL org.opencontainers.image.created=$BUILD_DATE
  LABEL org.opencontainers.image.description="customized scanservjs"
  LABEL org.opencontainers.image.revision=$EARTHLY_GIT_SHORT_HASH
  LABEL org.opencontainers.image.source="https://github.com/kpine/scanservjs-ocrmypdf"
  LABEL org.opencontainers.image.title="scanservjs"
  LABEL org.opencontainers.image.version=$SCANSERVJS_VERSION

build:
  FROM +image

  ARG EARTHLY_GIT_SHORT_HASH

  SAVE IMAGE --push ghcr.io/kpine/scanservjs:$EARTHLY_GIT_SHORT_HASH

release:
  FROM +image

  ARG EARTHLY_GIT_SHORT_HASH
  ARG EARTHLY_TARGET_TAG_DOCKER

  SAVE IMAGE --push ghcr.io/kpine/scanservjs:$EARTHLY_TARGET_TAG_DOCKER
  SAVE IMAGE --push ghcr.io/kpine/scanservjs:latest
