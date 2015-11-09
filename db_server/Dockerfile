FROM postgres
MAINTAINER franco.fiorese@gmail.com

LABEL io.docker-3tier-demo.image-specs="{\"Description\":\"Containerized PostgreSQL\",\"Version\":\"0.1\",\"app-service\":\"redmine.asc\", \"build command\":\"docker build -t f2hex/redmine .\"}"

ENV POSTGRES_PORT_5432_TCP=5432
ENV POSTGRES_USER=redmine
ENV POSTGRES_PASSWORD=secret
ENV POSTGRES_DB=redmine
