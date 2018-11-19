# Hands-on with containers

credit: “Extending AWS CodeBuild with Custom Build Environments” from AWS DevOps Blog

## Instructions

Over next 20 minutes, please execute instructions at following link and create PHP Container for CodeBuild with ECR: https://amzn.to/2QKYxN6

## Additional notes

Dockerfile that works:

```
FROM php:7

ARG composer_checksum=55d6ead61b29c7bdee5cccfb50076874187bd9f21f65d8991d46ec5cc90518f447387fb9f76ebae1fbbacf329e583e30
ARG composer_url=https://raw.githubusercontent.com/composer/getcomposer.org/ba0141a67b9bd1733409b71c28973f7901db201d/web/installer

ENV COMPOSER_ALLOW_SUPERUSER=1
ENV PATH=$PATH:vendor/bin

RUN apt-get update && apt-get install -y --no-install-recommends \
      curl \
      git \
      python-dev \
      python-pip \
      zlib1g-dev
RUN pip install --upgrade setuptools
RUN pip install awscli
RUN docker-php-ext-install zip
RUN curl -o installer "$composer_url"
RUN echo "$composer_checksum *installer" | shasum -c -a 384
RUN php installer --install-dir=/usr/local/bin --filename=composer
RUN rm -rf /var/lib/apt/lists/*
```
