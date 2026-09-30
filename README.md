# laravel-db-pull

Download a gzipped database dump from a server, using the variables of a GitHub environment. It runs on your machine and authenticates with your own SSH key. Nothing goes through GitHub Actions.

## Install

```bash
composer require --dev ivowermelinger/laravel-db-pull
```

## Usage

Run on the host (not inside the DDEV container), from anywhere in the project:

```bash
vendor/bin/pull-db PRODUCTION            # saves backups/production-<timestamp>.sql.gz
vendor/bin/pull-db BETA --import         # also snapshots and imports into DDEV
vendor/bin/pull-db BETA --repo org/repo --out dumps
```

## How it works

1. `gh variable list` reads `SSH_HOST`, `SSH_USER`, `SSH_PORT` and `APP_PATH` from the repository and from `<ENVIRONMENT>`. Environment variables override repository ones.
2. It connects over SSH, reads the `DB_*` values from `.env` in `APP_PATH`, and streams `mysqldump | gzip` into the output file.
3. With `--import`, it runs `ddev snapshot` and then `ddev import-db`.

## Requirements

- [GitHub CLI](https://cli.github.com) (`gh`), logged in, with access to the repository's environment variables
- An SSH key authorized on the server
- `mysqldump` on the server, and MySQL/MariaDB credentials in the server's `.env`
- `backups*` in `.gitignore`, so dumps are not committed
