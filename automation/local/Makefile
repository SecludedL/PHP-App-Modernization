.PHONY: all
all: install-composer-deps install-phpunit set-var-permissions configure-db

.PHONY: install-composer-deps
install-composer-deps: vendor

vendor: composer.json $(wildcard composer.lock)
	composer validate
	composer install --no-interaction --optimize-autoloader

.PHONY: install-phpunit
install-phpunit:
	php ./bin/phpunit install

.PHONY: set-var-permissions
set-var-permissions:
	mkdir -p ./var
	chmod a+rwx -R ./var

.PHONY: configure-db
configure-db:
	php bin/console d:s:u --force
	php bin/console doctrine:fixtures:load --no-interaction