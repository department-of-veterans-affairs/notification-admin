FROM python:3.6-alpine

ENV PYTHONDONTWRITEBYTECODE 1

RUN apk update && apk add --no-cache ca-certificates && rm -rf /var/cache/apk/*
COPY ./certs/VA-Internal-S2-RCA1-v1.crt /usr/local/share/ca-certificates/VA-Internal-S2-RCA1-v1.crt
RUN update-ca-certificates

ENV REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt

RUN apk add --no-cache bash build-base libxml2-dev libxslt-dev git nodejs npm g++ make libffi-dev && rm -rf /var/cache/apk/*

# update pip
RUN python -m pip install wheel

# -- Install Application into container:
RUN set -ex && mkdir /app

WORKDIR /app

COPY . /app

RUN set -ex && pip3 install -r requirements.txt --use-deprecated=legacy-resolver

RUN npm install
RUN npm run build

RUN make generate-version-file

ENV PORT=6012

ARG GIT_SHA
ENV GIT_SHA ${GIT_SHA}

CMD ["sh", "-c", "gunicorn -c gunicorn_config.py application"]