FROM node:12-alpine

RUN addgroup -g 10001 app && \
    adduser -D -G app -h /app -u 10001 app

WORKDIR /app

# S3 bucket in Cloud Services prod IAM
ADD https://s3.amazonaws.com/dumb-init-dist/v1.2.0/dumb-init_1.2.0_amd64 /usr/local/bin/dumb-init
RUN chmod +x /usr/local/bin/dumb-init
ENTRYPOINT ["/usr/local/bin/dumb-init", "--"]

RUN apk add --no-cache git

USER app
COPY --chown=app:app fxa-content-server /app
RUN npm ci && rm -rf ~/.cache ~/.npm


COPY --chown=app:app ["fxa-geodb", "../fxa-geodb/"]
WORKDIR /fxa-geodb
USER root
RUN chown -R app:app /fxa-geodb
USER app
RUN npm ci

COPY --chown=app:app ["fxa-shared", "../fxa-shared/"]
WORKDIR /fxa-shared
USER root
RUN chown -R app:app /fxa-shared
USER app
RUN npm ci

WORKDIR /app
RUN npm run build-production
