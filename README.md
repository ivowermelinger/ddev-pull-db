# ddev-pull-db

Import the database of a server into your local DDEV project, using the variables of a GitHub environment. The dump is streamed over SSH straight into DDEV, so no dump file is stored on your machine. It runs on your machine and authenticates with your own SSH key. Nothing goes through GitHub Actions.

## Install

```bash
ddev add-on get ivowermelinger/ddev-pull-db
```

Commit `.ddev/commands/host/pull-db` so your team gets the command. To update, run `ddev add-on get` again. To remove, run `ddev add-on remove pull-db`.

## Usage

It runs on the host (not inside the DDEV container), from anywhere in the project:

```bash
ddev pull-db PRODUCTION
ddev pull-db BETA
```

A `ddev snapshot` named `pre-pull-<timestamp>` is taken first. To go back, run `ddev snapshot restore pre-pull-<timestamp>`.

## How it works

1. `gh variable list` reads `SSH_HOST`, `SSH_USER`, `SSH_PORT` and `APP_PATH` from the repository and from `<ENVIRONMENT>`. Environment variables override repository ones.
2. It takes a `ddev snapshot` of the local database.
3. It connects over SSH, reads the `DB_*` values from `.env` in `APP_PATH`, and streams `mysqldump | gzip` into `ddev import-db`.

## Requirements

- DDEV v1.24 or newer
- [GitHub CLI](https://cli.github.com) (`gh`), logged in, with access to the repository's environment variables
- An SSH key authorized on the server
- `mysqldump` on the server, and MySQL/MariaDB credentials in the server's `.env`
