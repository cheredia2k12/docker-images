FROM alpine:3.12 as source

RUN apk add --no-cache --no-progress \
    git

ARG SHA
RUN git clone --branch py3 --depth=1 https://github.com/HelloZeroNet/ZeroNet.git /app/

WORKDIR /app

#------------------#

FROM supersandro2000/base-alpine:3.12

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

RUN export user=zeronet \
  && addgroup -S $user && adduser -D -S -G $user $user

RUN apk add --no-cache --no-progress \
    openssl \
    py3-pip \
    tor

COPY [ "files/pip.conf", "/etc/" ]
COPY [ "files/entrypoint.sh", "/usr/local/bin/" ]
COPY [ "files/run.sh", "/usr/local/bin/" ]
COPY --from=source [ "/app", "/app" ]

SHELL ["/bin/ash", "-eo", "pipefail", "-c"]
RUN pip3 install --no-cache-dir --progress-bar off -r /app/requirements.txt \
  # downgrade gevent until startup crash is fixed
  && pip3 install 'gevent<20.5.1' \
  && mv /app/plugins/disabled-UiPassword /app/plugins/UiPassword \
  && echo "ControlPort 9051" >>/etc/tor/torrc \
  && echo "CookieAuthentication 1" >>/etc/tor/torrc

VOLUME /app/data
WORKDIR /app
EXPOSE 43110 26552
ENV HOME=/app \
  ENABLE_TOR=false
ENTRYPOINT [ "entrypoint.sh" ]
CMD [ "run.sh" ]
