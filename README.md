# Matomo (aka Piwik) on Heroku, Dokku, or Flynn

Even though the application has been renamed from Piwik to Matomo, the most of the composer packages are still namespaced using `piwik`. This is being updated for Matomo 4 (https://github.com/matomo-org/matomo/issues/12519).

## Installation

1. Create a new app.
2. Connect a mysql database to the app.
3. Update config for database connection in `config.ini.php` as needed
4. Add config settings for `SALT` and `TRUSTED_HOST` (the domain name you access the Matomo app from)
5. Deploy app

## Config

The filesystem is ephemeral and will be wiped out on each deploy. Any settings that Matomo automatically updates in `vendor/piwik/piwik/config/config.ini.php` need to be manually duplicated in the `config.ini.php` in the repo, or they will be wiped out the next time the app is rebuilt.

## Plugins

Plugins are managed by composer. Plugins installed through Matomo's web interface will get erased when the app is rebuilt.

Most Matomo plugins do not include a composer.json file so you'll need to use the package option and define the settings for each plugin manually. See the configuration for the SecurityInfo plugin in `composer.json` for an example. By sure that `"type": "piwik-plugin"` is defined in the plugins package.json file.

1. `composer require [plugin]` to install the plugin
2. Add plugin to `Plugins[]` and `PluginsInstalled[]` lists in `config.ini.php` so that the plugin is activated when the app is re-deployed.

## GeoIP

This setup is configured to use the GeoIp2 plugin included in the core Matomo package. The GeoLite databases are downloaded using a custom buildpack https://github.com/danstiner/heroku-buildpack-geoip-geolite2 defined in `.buildpacks`.

You can turn on this geolocation method on in Settings > System > Geolocation. Rebuilding the app will get a fresh copy of the GeoLite databases. You can also configure the plugin to download an updated database periodically.

## Heroku

Heroku no longer uses the `.buildpacks` file, so you will need to specify buildpacks using the heroku cli.

[Install the cli](https://devcenter.heroku.com/articles/heroku-cli) if needed, then run the following to specify the buildpacks for your app:

```
heroku buildpacks:add --index 1 https://github.com/danstiner/heroku-buildpack-geoip-geolite2
heroku buildpacks:add --index 2 heroku/php
```
