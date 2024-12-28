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
    RAILS_ENV=production \
    BUNDLE_WITHOUT="development"

# Use secrets for sensitive variables during build
RUN --mount=type=secret,id=RAILS_MASTER_KEY \
    --mount=type=secret,id=POSTGRES_PASSWORD \
    --mount=type=secret,id=LETSENCRYPT_HOST \
    --mount=type=secret,id=LETSENCRYPT_TEST \
    --mount=type=secret,id=VIRTUAL_HOST \
    --mount=type=secret,id=VIRTUAL_PORT \
    --mount=type=secret,id=POSTGRES_HOST \
    --mount=type=secret,id=POSTGRES_DB \
    --mount=type=secret,id=POSTGRES_USER \
    --mount=type=secret,id=LETSENCRYPT_EMAIL \
    RAILS_MASTER_KEY=$(cat /run/secrets/RAILS_MASTER_KEY) \
    POSTGRES_PASSWORD=$(cat /run/secrets/POSTGRES_PASSWORD) \
    LETSENCRYPT_EMAIL=$(cat /run/secrets/LETSENCRYPT_EMAIL) \
    VIRTUAL_HOST=$(cat /run/secrets/VIRTUAL_HOST) \
    VIRTUAL_PORT=$(cat /run/secrets/VIRTUAL_PORT) \
    POSTGRES_HOST=$(cat /run/secrets/POSTGRES_HOST) \
    POSTGRES_DB=$(cat /run/secrets/POSTGRES_DB) \
    POSTGRES_USER=$(cat /run/secrets/POSTGRES_USER) \
    LETSENCRYPT_HOST=$(cat /run/secrets/LETSENCRYPT_HOST) \
    LETSENCRYPT_TEST=$(cat /run/secrets/LETSENCRYPT_TEST) \
    echo "Secrets loaded successfully"

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
EXPOSE 3000:3000
CMD ["./bin/rails", "server"]
