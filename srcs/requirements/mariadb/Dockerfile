FROM	debian:bookworm

RUN		apt-get update -y && apt-get upgrade -y \
		&& apt-get install mariadb-server -y \
		&& rm -rf /var/lib/apt/lists/*

RUN mkdir -p /var/run/mysqld \
    && chown -R mysql:mysql /var/run/mysqld \
    && chmod 755 /var/run/mysqld

COPY	conf/50-server.cnf /etc/mysql/mariadb.conf.d/50-server.cnf
COPY	tools/database.sh /usr/local/bin/

RUN chmod +x /usr/local/bin/database.sh

EXPOSE	3306

ENTRYPOINT	["/usr/local/bin/database.sh"]

CMD		["mysqld"]
