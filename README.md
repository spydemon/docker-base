Base applications set used for mostly all docker-compose development environment I manage.

It contains two applications:
  * a nginx proxy that will dispatch localhost HTTP and HTTPS queries to the corresponding project,
  * a mailcatcher that allows you to test your application email without risking to send real ones.

# HTTPS Management

First step is to generate your root CA with the excelent [mkcert](https://github.com/FiloSottile/mkcert) tool:

```txt
mkcert -install
```

Second step is to generate a certificat that will handles domains managed by your application:

```txt
mkcert <domain>.local "*.<domain>.local"
```

You have now to move and rename generated files in the `config/ssl` folder of this project. Note that their names should be `<domain>.local.pem` and `<domain>.local.crt`. If generated files doesn't match, you will have to rename them.
  * Generated `<domain>.local.-key.pem` file has to be renamed `<domain>.local.key` in nginx SSL folder.
  * Generated `<domain>.local.pem` file has to be renamed `<domain>.local.crt` in nginx SSL folder.

The next step is to add the domain in the `config/traefik.yml` file. It can be added like that:

```txt
tls:
  certificates:
    - certFile: /ssl/<domain>.local.crt
      keyFile: /ssl/<domain>.local.key
```

Finally, you have to set those labels in the configuration of the `web` container in the `docker-compose.yml` file of your project:

```txt
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.<project>-web.rule=Host(`<domain>.local`)"
  - "traefik.http.routers.<project>-web.tls.domains[0].main=<domain>.local"
  - "traefik.http.routers.<project>-web.tls.domains[0].sans=*.<domain>.local"
```