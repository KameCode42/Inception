FROM	debian:bookworm

RUN		apt-get update \
		&& apt-get install nginx -y \
		&& apt-get install vim -y \
		&& apt-get install curl -y \
		&& mkdir -p /etc/nginx/ssl \
		&& apt-get install openssl -y \
		&& openssl req -x509 -nodes -out /etc/nginx/ssl/inception.crt -keyout /etc/nginx/ssl/inception.key -subj "/C=FR/ST=IDF/L=Renens/O=42/OU=42/CN=dle-fur.42.fr/UID=dle-fur" \
		&& mkdir -p /var/run/nginx \
		&& chmod 755 /var/www/html \
		&& chown -R www-data:www-data /var/www/html \
		&& rm -rf /var/lib/apt/lists/*

COPY	conf/nginx.conf /etc/nginx/nginx.conf

EXPOSE	443

CMD 	["nginx", "-g", "daemon off;"]
