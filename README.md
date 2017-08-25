# Piwik on Dokku

## Installation

1. Create a new app with dokku.
2. Connect a mysql database to the app.
3. Add dokku config settings for the database connection: `DB_NAME`, `DB_HOST`, `DB_PORT` `DB_USERNAME`, and `DB_PASSWORD`.
4. Add dokku config settings for `SALT` and `TRUSTED_HOST` (the domain name you access the piwik app from)
5. Deploy repo to dokku

## Config

Dokku's filesystem is ephemeral and will be wiped out on each deploy. Any settings that piwik automatically updates in `vendor/piwik/piwik/config/config.ini.php` need to be manually duplicated in the `config.ini.php` in the repo, or they will be wiped out the next time the app is rebuilt.

## Plugins

Plugins are managed by composer. Plugins installed through piwik's web interface will get erased when the app is rebuilt.

Most piwik plugins do not include a composer.json file so you'll need to use the package option and define the settings for each plugin manually. See the configuration for the SecurityInfo plugin in `composer.json` for an example. By sure that `"type": "piwik-plugin"` is defined in the plugins package.json file.

1. `composer require [plugin]` to install the plugin
2. Add plugin to `Plugins[]` and `PluginsInstalled[]` lists in `config.ini.php` so that the plugin is activated when the app is re-deployed.

## GeoIP

This setup is configured to use the [GeoIP2 Plugin](https://github.com/diabl0/piwik-geoip2). The GeoLite databases are provided by a custom buildpack https://github.com/danstiner/heroku-buildpack-geoip-geolite2.git defined in `.buildpacks`.

You can turn on this geolocation method on in Settings > System > Geolocation. Rebuilding the app will get a fresh copy of the GeoLite databases.
