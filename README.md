# ddev-pull-db

Download a gzipped database dump from a server, using the variables of a GitHub environment. It runs on your machine and authenticates with your own SSH key. Nothing goes through GitHub Actions.

## Install

```bash
ddev add-on get ivowermelinger/ddev-pull-db
```

Commit `.ddev/commands/host/pull-db` so your team gets the command. To update, run `ddev add-on get` again. To remove, run `ddev add-on remove pull-db`.

## Usage

It runs on the host (not inside the DDEV container), from anywhere in the project:

```bash
ddev pull-db PRODUCTION            # saves backups/production-<timestamp>.sql.gz
ddev pull-db BETA --import         # also snapshots and imports into DDEV
ddev pull-db BETA --out dumps
```

## How it works

1. `gh variable list` reads `SSH_HOST`, `SSH_USER`, `SSH_PORT` and `APP_PATH` from the repository and from `<ENVIRONMENT>`. Environment variables override repository ones.
2. It connects over SSH, reads the `DB_*` values from `.env` in `APP_PATH`, and streams `mysqldump | gzip` into the output file.
3. With `--import`, it runs `ddev snapshot` and then `ddev import-db`.

## Requirements

- DDEV v1.24 or newer
- [GitHub CLI](https://cli.github.com) (`gh`), logged in, with access to the repository's environment variables
- An SSH key authorized on the server
- `mysqldump` on the server, and MySQL/MariaDB credentials in the server's `.env`
- `backups*` in `.gitignore`, so dumps are not committed
