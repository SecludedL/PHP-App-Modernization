PHP Demo Application
========================

The "Symfony Demo Application" is a reference application created to show how
to develop applications following the [Symfony Best Practices](https://symfony.com/doc/current/best_practices.html).

You can also learn about these practices in [the official Symfony Book][5].

Requirements
------------
  * PHP 8.0.2 or higher;
  * PDO-SQLite PHP extension enabled;
  * and the [usual Symfony application requirements](https://symfony.com/doc/current/setup.html#technical-requirements).

Local usage
-------------
  * Build the app container: `docker-compose build`
  * Run the containers: `docker-compose up -d`
  * Make sure the app runs: `docker-compose logs php`
  * Install the dependencies: `docker-compose exec php make -f automation/local/Makefile`

Then access the application in your browser at the given URL (<https://localhost:8000> by default).

If you don't have the Symfony binary installed, run `php -S localhost:8000 -t public/` to use the built-in PHP web server or [configure a web server](https://symfony.com/doc/current/setup/web_server_configuration.html) like Nginx or Apache to run the application.
