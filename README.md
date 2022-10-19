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
* Provide values for environment variables in [.env file](./docker/.env)

  ```shell
  DJANGO_SECRET_KEY=<random passphrase with at minimum 32 characters length>
  POSTGRES_PASSWORD=<database password>
  DATABASE_URL=postgres://entirety:<database password again>@entirety-db:5432/entirety

  CB_URL=<orion context broker url>
  IOTA_URL=<iot agent url>
  QL_URL=<quantumleap url>

  WEB_URL=<url under which the application will be accessible>
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
docker-compose -f docker-compose.yml pull
docker-compose -f docker-compose.yml up -d
```

### Add admin user (local auth only)

```bash
docker exec -it entirety-entirety-1 bash
python3 manage.py createsuperuser
```