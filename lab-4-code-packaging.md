# Hands-on with containers

credit: [“Extending AWS CodeBuild with Custom Build Environments”](https://aws.amazon.com/blogs/devops/extending-aws-codebuild-with-custom-build-environments/) from AWS DevOps Blog

## Initial deployment

1) Clone the Git repository:

```
git clone https://github.com/luigibianchi/codebuild-images.git
cd codebuild-images
```

2) Create the CloudFormation stack using the template.yml file

```
aws cloudformation create-stack \
 --stack-name codebuild-php \
 --parameters ParameterKey=EnvName,ParameterValue=php \
 --template-body file://template.yml
 ```

## Do the rest of the lab

3) Display stack outputs:

```
aws cloudformation describe-stacks \
 --stack-name codebuild-php \
 --output table \
 --query Stacks[0].Outputs
```

After the stack has been created, CloudFormation will return two outputs:

BuildImageRepositoryUri: the URI of the Docker repository that will host our build environment image.
SourceCodeRepositoryCloneUrl: the clone URL of the Git repository that will host our sample PHP code.

4) Check the content of the Dockerfile:

```
FROM php:7

ARG composer_url=https://getcomposer.org/installer

ENV COMPOSER_ALLOW_SUPERUSER=1
ENV PATH=$PATH:vendor/bin

RUN apt-get update && apt-get install -y --no-install-recommends \
      curl \
      git \
      python-dev \
      python-pip \
      zlib1g-dev \
      libzip-dev
RUN pip install --upgrade setuptools
RUN pip install awscli
RUN docker-php-ext-install zip
RUN curl -o installer "$composer_url"
RUN php installer --install-dir=/usr/local/bin --filename=composer
RUN rm -rf /var/lib/apt/lists/*
```

5) Provide authentication details for our registry to the local Docker engine by executing the output of the login helper provided by the AWS CLI:

```
aws ecr get-login --no-include-email
```

6) Build and push the Docker image. We’ll use the repository URI returned in the CloudFormation stack output (BuildImageRepositoryUri) as the image tag:

```
cd php
docker build -t [BuildImageRepositoryUri] .
docker push [BuildImageRepositoryUri]
```

After running these commands, your Docker image is pushed into Amazon ECR and ready to build your project.

7) Continue the lab on step 'Configure the Git repository' from [the initial blog post](https://aws.amazon.com/blogs/devops/extending-aws-codebuild-with-custom-build-environments/)
