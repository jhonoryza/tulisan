# Platform as a Service untuk Laravel

| Platform               | Bisa deploy Laravel?  | Catatan utama                      |
| ---------------------- | --------------------- | ---------------------------------- |
| **Vercel**             | ❌ Tidak ideal        | Serverless, PHP bukan first-class  |
| **Netlify**            | ❌ Tidak ideal        | Sama seperti Vercel                |
| **Cloudflare Workers** | ❌ Tidak bisa         | Tidak support PHP runtime          |
| **Stormkit.io**        | ❌ Tidak bisa         | Fokus frontend                     |
| **Render.com**         | ✅ Bisa               | Full server (Docker / Web Service) |
| **Laravel Cloud**      | ✅ Bisa               | Full server (Docker / Web Service) |
| **Sherpa.sh**          | ⚠️ Bisa tapi terbatas | Shared hosting style               |
| **leapcell.io**        | ❌ Tidak bisa         | Node/Python/Go/Rust                |

## Deploy Laravel ke Vercel

1. Buat entry serverless `api/index.php`

```php
<?php

require __DIR__.'/../public/index.php';
```

Laravel akan tetap jalan dari `public/index.php`.

Buat file `.vercelignore` untuk ignore vendor directory ketika deployed

```
/vendor
```

2. Tambahkan `vercel.json` penjelasannya
   [disini](https://vercel.com/docs/projects/project-configuration)

```json
{
  "version": 2,
  "framework": null,
  "builds": [
    {
      "src": "/api/index.php",
      "use": "vercel-php@0.8.0"
    },
    {
      "src": "/public/build/assets/**",
      "use": "@vercel/static"
    },
    {
      "src": "/public/**",
      "use": "@vercel/static"
    }
  ],
  // "functions": {
  //   "api/index.php": {
  //     "runtime": "vercel-php@0.8.0"
  //   }
  // },
  "routes": [
    {
      "src": "/build/assets/(.*)",
      "dest": "/public/build/assets/$1"
    },
    {
      "src": "/favicon.ico",
      "headers": {
        "Content-Type": "image/x-icon"
      },
      "dest": "/public/favicon.ico"
    },
    {
      "src": "/(.*)",
      "dest": "/api/index.php"
    }
  ],
  "outputDirectory": "public",
  "env": {
    "APP_NAME": "Your App Name",
    "APP_ENV": "production",
    "APP_DEBUG": "false",
    "APP_URL": "https://laravel-app.vercel.app",

    "LOG_CHANNEL": "stderr",
    "CACHE_DRIVER": "array",
    "SESSION_DRIVER": "array",

    "APP_CONFIG_CACHE": "/tmp/config.php",
    "APP_EVENTS_CACHE": "/tmp/events.php",
    "APP_PACKAGES_CACHE": "/tmp/packages.php",
    "APP_ROUTES_CACHE": "/tmp/routes.php",
    "APP_SERVICES_CACHE": "/tmp/services.php",
    "VIEW_COMPILED_PATH": "/tmp"
  }
}
```

3. PHP Runtime

- tidak mendukung Docker
- tidak mendukung custom runtime arbitrary
- PHP hanya lewat: [vercel-php](https://github.com/vercel-community/php) runtime

4. Optimize Laravel untuk Serverless

Di AppServiceProvider:

```php
<?php
public function register()
{
    $this->app->singleton(
        \Illuminate\Contracts\Http\Kernel::class,
        \App\Http\Kernel::class
    );
}
```

Dan jalankan:

```bash
composer install --optimize-autoloader --no-dev
php artisan config:clear
php artisan route:clear
```

## Deploy Laravel ke Netlify

1. Buat function entry `netlify/functions/index.php`

```php
<?php

require_once __DIR__.'/../../public/index.php';
```

2. Tambahkan `netlify.toml`

```toml
[build]
  command = "composer install --no-dev --optimize-autoloader"
  functions = "netlify/functions"
  publish = "public"

[[redirects]]
  from = "/*"
  to = "/.netlify/functions/index"
  status = 200
```

3. Runtime PHP

Netlify tidak native PHP, jadi kamu perlu PHP runtime via build image:

Biasanya pakai:

- netlify-labs/php (experimental)

Contoh (experimental):

```toml
[build.environment]
  PHP_VERSION = "8.2"
```

- atau custom build image (Docker)

```dockerfile
FROM php:8.2-cli

RUN apt-get update && apt-get install -y \
    git unzip libpq-dev \
    && docker-php-ext-install pdo_pgsql

WORKDIR /app
COPY . .
RUN composer install --no-dev --optimize-autoloader
```

```toml
[build]
  command = "docker build -t laravel-build ."
```

4. Contoh lengkap `netlify.toml`

Untuk `netlify-labs/php (experimental)`

```toml
########################################
# NETLIFY CONFIG FOR LARAVEL (PHP FaaS)
########################################

[build]
  # Install dependency Laravel
  command = """
    composer install \
      --no-dev \
      --optimize-autoloader
  """

  # Folder public Laravel
  publish = "public"

  # Lokasi serverless functions
  functions = "netlify/functions"


########################################
# BUILD ENVIRONMENT
########################################
[build.environment]
  # PHP runtime (experimental)
  PHP_VERSION = "8.2"

  # Composer performance
  COMPOSER_NO_INTERACTION = "1"
  COMPOSER_PROCESS_TIMEOUT = "2000"

  # Laravel env
  APP_ENV = "production"
  APP_DEBUG = "false"


########################################
# REDIRECT SEMUA REQUEST KE LARAVEL
########################################
[[redirects]]
  from = "/*"
  to = "/.netlify/functions/index"
  status = 200
  force = true


########################################
# FUNCTION SETTINGS
########################################
[functions]
  node_bundler = "esbuild"

  # Timeout function (maks 10s free, 26s pro)
  timeout = 10


########################################
# CONTEXT: PRODUCTION
########################################
[context.production.environment]
  APP_ENV = "production"
  LOG_CHANNEL = "stderr"


########################################
# CONTEXT: PREVIEW
########################################
[context.deploy-preview.environment]
  APP_ENV = "staging"
  APP_DEBUG = "true"


########################################
# CONTEXT: LOCAL DEV (netlify dev)
########################################
[context.dev.environment]
  APP_ENV = "local"
  APP_DEBUG = "true"
```

Untuk `custom build image (Docker)`

Buat file `docker/Dockerfile.build`

```dockerfile
FROM php:8.2-cli

# System deps
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libpq-dev \
    libzip-dev \
    zip \
    curl \
    && docker-php-ext-install \
        pdo \
        pdo_pgsql \
        zip

# Install Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /app

# Copy source
COPY . .

# Install dependency Laravel
RUN composer install \
    --no-dev \
    --optimize-autoloader \
    --no-interaction

# Laravel optimization
RUN php artisan key:generate --force || true
RUN php artisan config:clear
RUN php artisan route:clear
RUN php artisan view:clear
```

Kenapa || true?

- Karena APP_KEY biasanya diset via Netlify ENV
- Biar build nggak gagal

```toml
########################################
# NETLIFY + DOCKER BUILD (PHP/LARAVEL)
########################################

[build]
  command = """
    docker build \
      -f docker/Dockerfile.build \
      -t laravel-build .

    # Copy hasil build ke workspace
    docker create --name extract laravel-build
    docker cp extract:/app/vendor ./vendor
    docker cp extract:/app/bootstrap/cache ./bootstrap/cache
    docker rm extract
  """

  publish = "public"
  functions = "netlify/functions"


########################################
# BUILD ENV
########################################
[build.environment]
  PHP_VERSION = "8.2"
  DOCKER_BUILDKIT = "1"

  COMPOSER_NO_INTERACTION = "1"
  COMPOSER_PROCESS_TIMEOUT = "2000"

  APP_ENV = "production"
  APP_DEBUG = "false"


########################################
# REDIRECT KE LARAVEL
########################################
[[redirects]]
  from = "/*"
  to = "/.netlify/functions/index"
  status = 200
  force = true


########################################
# FUNCTION CONFIG
########################################
[functions]
  timeout = 10


########################################
# CONTEXT OVERRIDES
########################################
[context.deploy-preview.environment]
  APP_ENV = "staging"
  APP_DEBUG = "true"

[context.dev.environment]
  APP_ENV = "local"
  APP_DEBUG = "true"
```

## Deploy Laravel ke Render

1. Buat file `docker/Dockerfile`

```dockerfile
FROM php:8.2-fpm-alpine

# System deps
RUN apk add --no-cache \
    nginx \
    supervisor \
    bash \
    icu-dev \
    libzip-dev \
    postgresql-dev \
    oniguruma-dev

# PHP extensions
RUN docker-php-ext-install \
    pdo \
    pdo_pgsql \
    intl \
    zip \
    opcache

# Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

# Copy source
COPY . .

# Install deps
RUN composer install \
    --no-dev \
    --optimize-autoloader

# Permission
RUN chown -R www-data:www-data storage bootstrap/cache

# Nginx config
COPY docker/nginx.conf /etc/nginx/nginx.conf

EXPOSE 10000

CMD ["sh", "-c", "php-fpm & nginx -g 'daemon off;'"]
```

2. Buat file `docker/nginx.conf`

```nginx
worker_processes auto;

events { worker_connections 1024; }

http {
  include       mime.types;
  default_type  application/octet-stream;

  server {
    listen 10000;
    root /var/www/html/public;
    index index.php;

    location / {
      try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
      fastcgi_pass   127.0.0.1:9000;
      fastcgi_index  index.php;
      include        fastcgi_params;
      fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
  }
}
```

3. Create Render Web Service

- Type: Web Service
- Environment: Docker
- Dockerfile path: docker/Dockerfile
- Port: 10000

4. ENV Variable (Dashboard)
5. Migration

```bash
php artisan migrate --force
```

Jalankan via: Render Shell

## Deploy Laravel ke Sherpa.sh

1. Set Document Root ke: `/public`
2. Build / Deploy Command

Biasanya Sherpa kasih field Build Command atau Post-deploy Script.

```bash
composer install --no-dev --optimize-autoloader
php artisan key:generate --force
php artisan migrate --force
```

3. Set Environment Variable

Di dashboard Sherpa → Environment Variables

4. Permission Folder (WAJIB)

Sherpa biasanya tidak auto chmod.

```bash
chmod -R 775 storage bootstrap/cache
```
