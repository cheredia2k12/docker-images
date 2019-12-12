FROM supersandro2000/base-alpine:3.10

ARG BUILD_DATE
ARG VERSION
ARG REVISION

LABEL maintainer="Sandro Jäckel <sandro.jaeckel@gmail.com>" \
  org.opencontainers.image.created=$BUILD_DATE \
  org.opencontainers.image.authors="Sandro Jäckel <sandro.jaeckel@gmail.com>" \
  org.opencontainers.image.url="https://github.com/SuperSandro2000/docker-images/tree/master/zeronet" \
  org.opencontainers.image.documentation="https://zeronet.io/docs/" \
  org.opencontainers.image.source="https://github.com/SuperSandro2000/docker-images" \
  org.opencontainers.image.version=$VERSION \
  org.opencontainers.image.revision=$REVISION \
  org.opencontainers.image.vendor="SuperSandro2000" \
  org.opencontainers.image.licenses="GPL-2.0" \
  org.opencontainers.image.title="ZeroNet" \
  org.opencontainers.image.description="ZeroNet - Decentralized websites using Bitcoin crypto and BitTorrent network"

ENV HOME=/app ENABLE_TOR=false

RUN export user=zeronet \
  && addgroup -S $user && adduser -D -S -G $user $user

# TODO: When gevent 1.5 hits stable switch to gevent wheel again.
# The gevent wheel lags _corecffi in gevent.libuv which ZeroNet requires to run. See https://github.com/gevent/gevent/issues/1419
RUN apk add --no-cache --no-progress py3-gevent py3-pip tor

COPY [ "files/pip.conf", "/etc/" ]
COPY [ "files/entrypoint.sh", "/usr/local/bin/" ]
COPY [ "files/run.sh", "/usr/local/bin/" ]

SHELL ["/bin/ash", "-eo", "pipefail", "-c"]

RUN apk add --no-cache --no-progress tar \
  && mkdir /app \
  && wget -O- -- https://github.com/HelloZeroNet/ZeroNet/tarball/py3 | tar xz -f - -C /app --strip-components=1 \
  && pip3 install --no-cache-dir --progress-bar off -r /app/requirements.txt \
  && mv /app/plugins/disabled-UiPassword /app/plugins/UiPassword \
  && echo "ControlPort 9051" >>/etc/tor/torrc \
  && echo "CookieAuthentication 1" >>/etc/tor/torrc \
  && apk del tar

EXPOSE 43110 26552
VOLUME /app/data
WORKDIR /app
ENTRYPOINT [ "entrypoint.sh" ]
CMD [ "run.sh" ]