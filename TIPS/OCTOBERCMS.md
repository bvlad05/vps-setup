# OctoberCMS

## After first git pull
```sh
mkdir storage
mkdir storage/app
mkdir storage/app/media
mkdir storage/app/uploads
mkdir storage/cms
mkdir storage/cms/cache
mkdir storage/cms/combiner
mkdir storage/cms/twig
mkdir storage/framework
mkdir storage/framework/cache
mkdir storage/framework/sessions
mkdir storage/framework/views
mkdir storage/logs
touch storage/logs/system.log
mkdir storage/temp
mkdir storage/temp/media
mkdir storage/temp/public
```

## Build sh
```sh
#!/bin/sh

php artisan down
git pull origin develop
git submodule init
git submodule update
composer install --no-interaction --no-dev --prefer-dist
php artisan october:up
php artisan queue:restart
npm install
npm run prod
php artisan cache:clear
php artisan up
```