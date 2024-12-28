# syntax=docker/dockerfile:1.4

ARG RUBY_VERSION=3.3.1
FROM ruby:$RUBY_VERSION

# Install packages needed to build gems
RUN apt-get update -qq && \
    apt-get install -y build-essential libvips bash bash-completion libffi-dev tzdata postgresql nodejs npm yarn && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /usr/share/doc /usr/share/man

# Rails app lives here
WORKDIR /rails

# Set non-sensitive environment variables
ENV RAILS_LOG_TO_STDOUT="1" \
    RAILS_SERVE_STATIC_FILES="true" \
    BUNDLE_WITHOUT="development"

# Use secrets for sensitive variables during build
RUN --mount=type=secret,id=RAILS_MASTER_KEY \
    --mount=type=secret,id=POSTGRES_PASSWORD \
    --mount=type=secret,id=LETSENCRYPT_EMAIL \
    RAILS_MASTER_KEY=$(cat /run/secrets/RAILS_MASTER_KEY) \
    POSTGRES_PASSWORD=$(cat /run/secrets/POSTGRES_PASSWORD) \
    LETSENCRYPT_EMAIL=$(cat /run/secrets/LETSENCRYPT_EMAIL) \
    echo "Secrets loaded successfully"

# Set remaining environment variables
ENV VIRTUAL_HOST=another.tambu-tech.com \
    VIRTUAL_PORT=3001 \
    RAILS_ENV=production \
    POSTGRES_HOST=postgres \
    POSTGRES_DB=prod_2 \
    POSTGRES_USER=postgres \
    LETSENCRYPT_HOST=tambu-tech.com \
    LETSENCRYPT_TEST=false

# Install application gems
COPY Gemfile Gemfile.lock ./
RUN bundle install

# Copy application code
COPY . .

# Precompile assets for production
RUN SECRET_KEY_BASE_DUMMY=1 ./bin/rails assets:precompile

# Entrypoint prepares the database
ENTRYPOINT ["/rails/bin/docker-entrypoint"]

# Start the server by default
EXPOSE 3001
CMD ["./bin/rails", "server", "-p", "3001"]
