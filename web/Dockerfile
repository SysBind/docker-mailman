FROM python:2.7-alpine3.6

MAINTAINER Asaf Ohayon <asaf@sysbind.co.il>

# Add needed files for uwsgi server + settings for django
COPY mailman-web /opt/mailman-web
# Add startup script to container
COPY docker-entrypoint.sh /usr/local/bin/

# Install packages and dependencies for postorius and hyperkitty Add user for
# executing apps, change ownership for uwsgi+django files and set execution
# rights for management script
RUN set -ex \
	&& apk add --no-cache --virtual .build-deps gcc libc-dev linux-headers \
	   	postgresql-dev mariadb-dev \
	&& apk add --no-cache --virtual .mailman-rundeps bash sassc \
	   postgresql-client mysql-client py-mysqldb curl mailcap \
	&& pip install -U django==1.11 pip \
	&& pip install mailmanclient==3.1.1 \
		postorius==1.1.2 \
		hyperkitty==1.1.4 \
		django-mailman3==1.1.0 \
		whoosh \
		uwsgi \
		psycopg2 \
		dj-database-url \
		mysqlclient \
		typing \
		parse \
	&& apk del .build-deps \
	&& addgroup -S mailman \
	&& adduser -S -G mailman mailman \
	&& chown -R mailman /opt/mailman-web/ \
	&& chmod u+x /opt/mailman-web/manage.py

WORKDIR /opt/mailman-web

# Expose port 8000 for http and port 8080 for uwsgi
# (see web/mailman-web/uwsgi.ini#L2-L4)
EXPOSE 8000 8080

# Use stop signal for uwsgi server
STOPSIGNAL SIGINT

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["uwsgi", "--ini", "/opt/mailman-web/uwsgi.ini"]
