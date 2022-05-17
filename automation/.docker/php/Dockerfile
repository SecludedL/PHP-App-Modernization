FROM php:8.0-fpm-alpine3.15 as base

# Returns an error code even when any command in a pipe command chain fails
# For details, please see https://github.com/hadolint/hadolint/wiki/DL4006
SHELL ["/bin/ash", "-eo", "pipefail", "-c"]

# Add the PHP extension installler package
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

# - Add several common packages without storing them in the package cache (used to reduce image size)
RUN apk add --update --no-cache make \
    # - Install and enable several common PHP extensions: bcmatch, intl & zip (required by most frameworks)
    && chmod +x /usr/local/bin/install-php-extensions \
        && sync  \
        && install-php-extensions pdo_mysql intl zip bcmath redis apcu \
    && docker-php-ext-enable opcache

# Install the symfony CLI
RUN wget https://github.com/symfony-cli/symfony-cli/releases/download/v5.4.8/symfony-cli_5.4.8_x86.apk -O symfony.apk \
    && apk add --allow-untrusted ./symfony.apk

######################################
# Build upon the base image
# to generate the local/dev image
######################################
FROM base as dev

# Returns an error code even when any command in a pipe command chain fails
# For details, please see https://github.com/hadolint/hadolint/wiki/DL4006
SHELL ["/bin/ash", "-eo", "pipefail", "-c"]

ENV COMPOSER_MEMORY_LIMIT=-1

ARG MIN_NPM_VERSION="7.17.0"

# Install Composer and make it available globally, then install yarn and npm
RUN apk add --no-cache --virtual composer-deps curl \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && apk del composer-deps \
    && apk add --no-cache "npm>=${MIN_NPM_VERSION}"

# Create the required var directory and its sub-directories, then make them writeable
RUN set -xe \
 && mkdir -p var/cache \
 && mkdir -p var/log \
 && chmod -R a+rwx var

# Copy the composer.json and lock files to the container and install the dependencies
COPY composer.json composer.lock ./

# Install the packages and generate the optimized autoload file
RUN composer install --no-interaction --no-scripts --optimize-autoloader --no-progress ${COMPOSER_EXTRA_FLAGS}

# Now install the dependecies for front-end components
COPY package.json ./
RUN npm install

# Copy the source code to the container
# Please make sure this source path is relative to the build context
COPY . /var/www/html

CMD [ "symfony", "server:start", "--port=8080" ]

######################################
# Build upon the base image
# to generate the prod image
######################################
FROM base AS prod

COPY --from=build /var/www/html /var/www/html

CMD [ "symfony", "server:start", "--port=8080" ]