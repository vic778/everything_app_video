FROM postgres:14.2-alpine

ENV POSTGRES_DB=demodb_production
ENV POSTGRES_USER=postgres
ENV POSTGRES_PASSWORD=password

COPY ./postgres-data /var/lib/postgresql/data

EXPOSE 80

CMD ["postgres"]