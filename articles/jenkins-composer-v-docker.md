# Using Jenkins to build using composer

Checkout my [docker based jenkins container](https://github.com/sudh33ra/animated-couscous/blob/master/README.md)! 

## Set up the Dockerfile

You can have the Dockerfile in the project repo itself (Recommended) or you can have it in the jenkins. It can be something like this

```Dockerfile
FROM composer:1.7

COPY database/ database/ ; exit 0
COPY composer.json composer.json
COPY composer.lock composer.lock

RUN COMPOSER_ALLOW_SUPERUSER=1 composer install \
    --ignore-platform-reqs \
    --no-interaction \
    --no-plugins \
    --no-scripts \
    --no-dev \
    --prefer-dist
```

Note that `composer.lock` and `database/` might not be needed. Might not even work if they're there. Depends on your project.

If you're gonna using jenkins to hold and copy the Dockerfile, remember to add it to the pipeline or the freestyle project.

Setup the jenkins pipeline / the freestyle step to run on the following condition

```shell
#!/bin/bash

RUN_COMPILER=0 && git diff --name-only $GIT_PREVIOUS_COMMIT $GIT_COMMIT | grep -q 'composer.json' && RUN_COMPILER=1

if [ $RUN_COMPILER -eq 1 ]
then
  exit 0
else
  exit 1
fi
```

the step would be running this

```shell
docker build -t composer-image .
docker create -ti --name composer-container composer-image:latest bash
docker cp composer-container:/app/vendor/ vendor/
docker rm -fv composer-container
```

That's it! If all went well, jenkins will now create the vendor directory in all it's glory whenever you make a change in composer.json.

Notes : Remember, everything here is under the assumption that your base directory holds your code. If it's in a subdirectory, you will have to do this there.
