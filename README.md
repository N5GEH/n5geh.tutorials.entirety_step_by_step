# Deploy your own instance of Entirety

[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg)](https://conventionalcommits.org)

## Prerequisites

### Docker

* [Docker](https://docs.docker.com/engine/install/) is installed and ready to use

### N5GEH FIWARE platform

* You have already deployed the [N5GEH FIWARE platform](https://github.com/N5GEH/n5geh.platform) and the platform you
  are using to deploy Entirety has (network) access to it.

## Setup repository

* Either use git or download and extract repository files
  ```bash
  git clone https://github.com/N5GEH/n5geh.tutorials.entirety_step_by_step.git
  cd n5geh.tutorials.entirety_step_by_step
  cd docker
  ```
* In current folder (n5geh.tutorials.entirety_step_by_step/docker) create a `.env` file, which can be copied from [.env.EXAMPLE](./docker/.env.EXAMPLE). Following environment variables must be given correctly. Default values can be used for testing purposes.
  ```shell
  # Database setup
  POSTGRES_DB=<database name, default: entirety>
  POSTGRES_USER=<database user, default: entirety>
  POSTGRES_PASSWORD=<database password, default: postgrespw>
  DATABASE_HOST=<database host, default: entirety-db>
  DATABASE_PORT=<database port, default: 5432>

  ...

  # FIWARE
  CB_URL=<orion context broker url>
  IOTA_URL=<iot agent url>
  QL_URL=<quantumleap url>

  WEB_HOST=<host name or IP, under which the application will be accessible>
  ```

* If you're using oidc authentication also configure following settings

  ```shell
  OIDC_OP_AUTHORIZATION_ENDPOINT=https://<KeyCloak instance>/auth/realms/<realm name>/protocol/openid-connect/auth
  OIDC_OP_JWKS_ENDPOINT=https://<KeyCloak instance>/auth/realms/<realm name>/protocol/openid-connect/certs
  OIDC_OP_TOKEN_ENDPOINT=https://<KeyCloak instance>/auth/realms/<realm name>/protocol/openid-connect/token
  OIDC_OP_USER_ENDPOINT=https://<KeyCloak instance>/auth/realms/<realm name>/protocol/openid-connect/userinfo
  OIDC_RP_CLIENT_ID=<oidc client id>
  OIDC_RP_CLIENT_SECRET=<oidc client secret>
  ```

* For a full list of configuration options
  see [settings](https://github.com/N5GEH/n5geh.tools.entirety/blob/development/docs/SETTINGS.md)

## Run the stack

Replace compose file name with the one you are using.

```bash
docker compose -f docker-compose.yml pull
docker compose -f docker-compose.yml -p entirety up -d
```
After a few seconds, entirety should be accessible via **http://< host >:80**, for example [http://localhost:80](http://localhost:80)
> Note: Switch from ghcr.io/n5geh/n5geh.tools.entirety:*latest* to *development* to keep track of the newest features.
>
> If you are using ghcr (GitHub Container Registry) for the first time, you need to first sign in to the `ghcr.io`
> ```bash
> export CR_PAT=TOKEN
> echo $CR_PAT | docker login ghcr.io -u USERNAME --password-stdin
> ```
> `TOKEN` (classic [personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)) and `USERNAME` must be replaced with yours. Check [here](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry#authenticating-with-a-personal-access-token-classic) for more information.

### Add admin user (local auth only)

Normally, an admin user should already be created with user name: "**admin**" and password: "**admin**". If that is not the case, you can still create new admin user via:
```bash
docker exec -it entirety-entirety-1 bash
python3 manage.py createsuperuser
```

### Configure OIDC provider (OIDC auth only)

Basically, you can use an OIDC provider of your choice. However, in this tutorial we will use KeyCloak. Therefore, the
following is a configuration guide for KeyCloak.

**Create a new client** with following settings:

| Setting                   | Value                                                       |
|---------------------------|-------------------------------------------------------------|
| **Name**                  | entirety (or any name according to your naming conventions) |
| **Authorization Enabled** | ON                                                          |
| **valid redirect urls**   | http(s)://your application url/oidc/callback/*              |

**Add client roles** with **composite** to next role in list:

* super_admin
* server_admin
* project_admin
* user

Either add users to roles, or create groups and assign them.

