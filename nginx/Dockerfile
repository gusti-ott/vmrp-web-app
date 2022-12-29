FROM nginx:1.15.8

# Install certbot for letsencrypt certificates
# Install also an init handler for alpine cron
RUN apk add --no-cache certbot openrc busybox-initscript

# Replace existing files by our own configs
RUN rm /etc/nginx/nginx.conf && rm /etc/nginx/conf.d/default.conf
COPY nginx.conf /etc/nginx/
COPY ./conf.d/flask_app.conf /etc/nginx/conf.d/

ARG DOMAIN
ARG FLASK
ARG EMAIL

ENV DOMAIN=sasim.mcube-cluster.de
ENV FLASK=flask_app
ENV EMAIL=gusztav.ottrubay@tum.de
ENV DOLLAR='$'

# Substitute contents and write a new application.conf file, which is imported
# by nginx.conf
RUN envsubst \
      </etc/nginx/conf.d/flask_app.conf \
      >/etc/nginx/conf.d/application.conf \
  && rm /etc/nginx/conf.d/flask_app.conf

# Directory needed for LetEncrypt certificates renewal
RUN mkdir /var/lib/certbot

# App entrypoint and auto-renewal scripts
COPY ./bin/entrypoint.sh /entrypoint.sh
COPY ./bin/renew /etc/periodic/weekly/renew
RUN chmod +x /entrypoint.sh \
  && chmod +x /etc/periodic/weekly/renew

# Install certificates and launch
ENTRYPOINT ../entrypoint.sh $DOMAIN $EMAIL