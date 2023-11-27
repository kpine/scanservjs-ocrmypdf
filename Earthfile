VERSION 0.7

ARG SCANSERVJS_VERSION=v3.0.3

FROM docker.io/sbs20/scanservjs:$SCANSERVJS_VERSION

RUN apt-get update && apt-get install -y --no-install-recommends \
      ocrmypdf

jbig2enc:
  RUN apt-get update && apt-get install -y --no-install-recommends \
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
  RUN apt-get update && apt-get install -y --no-install-recommends \
        python3-venv

  ARG OCRMYPDF_VERSION=15.4.3

  RUN python3 -m venv /venv \
   && /venv/bin/pip install ocrmypdf==$OCRMYPDF_VERSION

  SAVE ARTIFACT /venv

docker:
  COPY +ocrmypdf/venv /venv
  COPY +jbig2enc/build /usr/local

  ENV PATH "/venv/bin:$PATH"

  SAVE IMAGE --push ghcr.io/kpine/scanservjs:latest
