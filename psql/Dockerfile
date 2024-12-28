FROM postgres:14.2-alpine

ENV POSTGRES_DB=demodb_production
ENV POSTGRES_USER=postgres
RUN --mount=type=secret,id=POSTGRES_PASSWORD \
    echo "./private-install-script --access-token $(cat /run/secrets/POSTGRES_PASSWORD)"

COPY ./postgres-data /var/lib/postgresql/data

EXPOSE 80

CMD ["postgres"]
