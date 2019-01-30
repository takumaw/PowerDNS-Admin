FROM node:10 as node-builder

COPY package.json .yarnrc /powerdns-admin/

RUN set -ex \
    && cd /powerdns-admin \
    && yarn install


FROM python:3.5

ARG ENVIRONMENT=development
ENV FLASK_APP=/powerdns-admin/app/__init__.py

WORKDIR /powerdns-admin

COPY . /powerdns-admin/
COPY --from=node-builder /powerdns-admin/app/static/node_modules /powerdns-admin/app/static/node_modules

RUN set -ex \
	&& apt-get update && apt-get install -y --no-install-recommends \
        libffi-dev \
        libldap2-dev \
        libsasl2-dev \
        libssl-dev \
        libxml2-dev \
        libxmlsec1-dev \
        libxslt1-dev \
        mysql-client \
        netcat \
        supervisor \
    && rm -rf /var/lib/apt/lists/* \
    && pip3 install -r requirements.txt \
    && mkdir -p logs \
    && cp configs/${ENVIRONMENT}.py /powerdns-admin/config.py \
    && flask assets build \
    && chown -R www-data:www-data \
        app/static \
        logs \
    && mv supervisord.conf /etc/supervisord.conf \
    && mv docker/entrypoint.sh /entrypoint.sh \
    && rm -rf \
        .git

VOLUME /powerdns-admin/logs

ENTRYPOINT ["/entrypoint.sh"]
