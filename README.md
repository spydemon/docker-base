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

Finally, you have to move and rename generated files in the `config/ssl` folder of this project. Note that their name should be `<domain>.local.pem` and `<domain>.local.crt`. If generated files doesn't match those name, you have to rename since jwilder proxy use them for knowing which ones to use.
  * Generated `<domain>.local.-key.pem` file has to be renamed `<domain>.local.key` in nginx SSL folder.
  * Generated `<domain>.local.pem` file has to be renamed `<domain>.local.crt` in nginx SSL folder.