# org-keycloak

This repo has 'development' and more 'production-like' configs for Keycloak. We should use the version of Keycloak maintained by the Systems team
for real use cases - however these configs are still useful for setting up prototype environments.

The 'dev' environment should only be used on your machine. The 'production-like' one should be secure enough for an internal cloud setting, but shouldn't be used with 'real' user's credentials.

## Development Environment ##

To run the dev environment you can do:

```bash
cp infra/dev.txt .env
```

and

```bash
podman-compose up -d
```

(replacing `podman-compose` with `docker compose` as needed).

This will run Keycloak in 'dev' mode, without https nor strict checks on host claims in client supplied headers.

Once the server is up you can go to: http://localhost:8080 and log in with username `admin` and password `admin`. There you can create a new Realm to work with, register a Client in the Realm and add new users.

Once you are finished you can do:

``` bash
podman-compose down -d
```

## Production-like Environment ##

The setup for a production-like environment is quite a bit more involved. Some things we need to do:

* run with a reverse-proxy, which will handle encryption
* only expose a minimal set of url paths to the public
* explicitly specify host urls, with a separate one for admin
* allow backchannel access for servers on the same local network
* throttle access requests via Realm settings
* set up monitoring and alerting

The file `infra/prod-like.txt` gives indicitative values for the Keycloak config to support this setup, with some explaining comments. You can use it, alongside the Keycloak docs, to build a production `.env` file. Similarly, the file `infra/nginx.conf` gives an example Nginx configuration. Assuming a running Nginx server you can run the Keycloak server with:

``` bash
podman-compose -f compose.prod.yml up -d
```

Once logged into the Admin portal you should also throttle Realm access requests. Ideally this is done at the reverse proxy level also.





