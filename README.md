# SQL Server Driver for PHP Buildpack

This Cloud Native Buildpack installs the Microsoft SQLSRV and PDO_SQLSRV PHP extensions together with Microsoft ODBC Driver 18 for SQL Server on Linux amd64.

## Buildpack order

Run this buildpack after `heroku/php`, because it uses the PHP runtime and `php-config` provided by the PHP buildpack. Run `heroku/procfile` after it when the application uses a `Procfile`.

The buildpack currently supports PHP 8.3, 8.4, and 8.5 non-thread-safe extension artifacts from `msphpsql` 5.13.2.

## Runtime configuration

The buildpack stores the ODBC driver, its `msodbcsqlr18.rll` resource file, and its registration file in the CNB launch layer. The driver and resource keep the relative `lib64/../share/resources/en_US/` layout required by Microsoft ODBC Driver 18.

It exposes:

- `LD_LIBRARY_PATH`, pointing to the PHP extension directory containing unixODBC and `libmsodbcsql`;
- `ODBCSYSINI`, pointing to the directory containing the generated `odbcinst.ini`;
- `ODBCINSTINI=odbcinst.ini`, the configuration filename resolved relative to `ODBCSYSINI`.

`ODBCINSTINI` must remain a filename, not an absolute path. unixODBC ignores an absolute value in this configuration.

## Deployment

Use this repository as a standalone custom buildpack together with the official Heroku PHP CNB. The buildpack version is incremented in `buildpack.toml` when its runtime contents or environment configuration changes so that a builder does not reuse an older buildpack artifact.

After deployment, verify that both PHP extensions are loaded:

```sh
php -m | grep -E '^(pdo_sqlsrv|sqlsrv)$'
```