# README.md

## Instalando por primera vez

Se tienen dos archivos `default.conf.template` (por defecto para usar) y `default.conf-with-ssl.template`.

Como es la instalación por primera vez, o en caso de haberse eliminado los volúmenes que contenía los certificados de Let's Encrypt, es necesario que primero se levante el docker solo en http sin ssl.

### Levantamos únicamente el `nginx-waf`

```bash
sudo docker compose up -d nginx-waf
```

### Corremos el certbot por primera vez

```bash
sudo docker compose run --rm certbot   certonly   --webroot   --webroot-path=/var/www/certbot   -d khrix.com.ar -d www.khrix.com.ar   --agree-tos   -m cmaccarino97@gmail.com   --non-interactive
```

### Damos acceso a los archivos generados

Para eso usaremos un contenedor temporal

```bash
sudo docker run --rm -it   -u 0:0   -v server_certbot-etc:/etc/letsencrypt   alpine   sh -c "chmod -R a+rX /etc/letsencrypt && ls -l /etc/letsencrypt && ls -l /etc/letsencrypt/live"
```

### Paramos el servicio

```bash
sudo docker compose down
```

### Cambiamos el archivo de configuración de nginx a usar

```bash
mv default.conf.template default.conf.template.backup
mv default.conf-with-ssl.template default.conf.template
```

### Corremos todo (se habilitará el SSL)
```bash
sudo docker compose up -d
```

## Verificar errores

Para eso usaremos el siguiente comando

```bash
sudo docker logs nginx-waf --tail=50
```