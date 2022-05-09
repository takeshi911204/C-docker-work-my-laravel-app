FROM mysql/mysql-server:8.0

# カレントディレクトリの.envを優先します
ENV MYSQL_DATABASE=default_db_name \
  MYSQL_USER=root \
  MYSQL_PASSWORD=%DBz0x9c8v7 \
  MYSQL_ROOT_PASSWORD=%DBz0x9c8v7 \
  TZ=Asia/Tokyo

COPY ./my.cnf /etc/my.cnf
RUN chmod 644 /etc/my.cnf
