# PHP image to run dusk on Pipelines

[![Docker Pulls](https://img.shields.io/docker/pulls/zaherg/php-7.2-dusk-bitbucket-pipelines.svg)](https://hub.docker.com/r/zaherg/php-7.2-dusk-bitbucket-pipelines/) [![](https://images.microbadger.com/badges/image/zaherg/php-7.2-dusk-bitbucket-pipelines.svg)](https://microbadger.com/images/zaherg/php-7.2-dusk-bitbucket-pipelines "Get your own image badge on microbadger.com") [![](https://images.microbadger.com/badges/version/zaherg/php-7.2-dusk-bitbucket-pipelines.svg)](https://microbadger.com/images/zaherg/php-7.2-dusk-bitbucket-pipelines "Get your own version badge on microbadger.com") [![](https://images.microbadger.com/badges/commit/zaherg/php-7.2-dusk-bitbucket-pipelines.svg)](https://microbadger.com/images/zaherg/php-7.2-dusk-bitbucket-pipelines "Get your own commit badge on microbadger.com")

This is a simple PHP 7.2 image which contain just the minimum required to run Laravel Dusk on bitbucket pipelines.

You can read more about it from this post [https://mhdzaherghaibeh.name/2018/02/03/running-laravel-dusk-tests-on-bitbucket-the-easy-way/](https://mhdzaherghaibeh.name/2018/02/03/running-laravel-dusk-tests-on-bitbucket-the-easy-way/)

## bitbucket-pipeline example

This is just an example, the content of the file `.env.dusk` is not different that `.env.example` except that
the `APP_URL` value is `http://localhost:8000` since am using `artisan serve` command.

```
APP_URL=http://localhost:8000
```

Your `bitbucket-pipeline.yml` should look like this:


```
# This is a sample build configuration for PHP.
# Check our guides at https://confluence.atlassian.com/x/e8YWN for more examples.
# Only use spaces to indent your .yml configuration.
# -----
# You can specify a custom docker image from Docker Hub as your build environment.
image: zaherg/php-7.2-dusk-bitbucket-pipelines:latest


pipelines:
  default:
    - step:
        caches:
          - composer
        script:
          - cp .env.dusk .env
          - composer install --no-progress --no-suggest --prefer-dist
          - php artisan serve &
          - php artisan dusk
```

## Duck configuration:

To make it work, you will need to make sure that you add the argument `--no-sandbox` to your options so the function
should look like

```
    /**
     * Create the RemoteWebDriver instance.
     *
     * @return \Facebook\WebDriver\Remote\RemoteWebDriver
     */
    protected function driver()
    {
        $options = (new ChromeOptions)->addArguments([
            '--disable-gpu',
            '--headless',
            '--no-sandbox'
        ]);

        return RemoteWebDriver::create(
            'http://localhost:9515', DesiredCapabilities::chrome()->setCapability(
                ChromeOptions::CAPABILITY, $options
            )
        );
    }
```
