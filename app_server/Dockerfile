FROM redmine
MAINTAINER franco.fiorese@gmail.com

LABEL io.docker-3tier-demo.image-specs="{\"Description\":\"Containerized Redmine\",\"Version\":\"0.1\",\"app-service\":\"redmine.asc\", \"build command\":\"docker build -t f2hex/redmine .\"}"

ENV POSTGRES_PORT_5432_TCP=5432
ENV POSTGRES_ENV_POSTGRES_USER=redmine
ENV POSTGRES_ENV_POSTGRES_PASSWORD=secret
ENV POSTGRES_ENV_POSTGRES_DB=redmine
ENV REDMINE_DB_SERVER=redmine-db_server.redmine

VOLUME /usr/src/redmine/files
COPY docker-entrypoint.sh /
RUN chmod +x /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]
EXPOSE 3000
CMD ["rails", "server", "-b", "0.0.0.0"]

