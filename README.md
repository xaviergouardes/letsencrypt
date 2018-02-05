# ToDo

* docker compose pour l'exemple de reverse proxy avec docker-gen
* Positionner le répertoire de partage /tmp/nginx ailleur car il disparaitra au reboot - vérifier les autres répertoires de partage.
* Passer whoami en alpine 3.7 pour homogeeniser

# letsencrypt
letsencrypt, nginx-proxy pour arm Odroid

## Container LetsEncrypt companion
Ressources web :
* https://github.com/jwilder/docker-letsencrypt-nginx-proxy-companion
* https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion

Construction de l'image
```
docker build -t xtechnologies.letsencrypt-companion.arm64v8
```

Lancement des trois containers : nginx, docker-gen et letsencrypt-companion. Pour tester ajouter `-e "LETSENCRYPT_TEST=true" \` au container letsencrypt
```
docker run -d -p 80:80 -p 443:443 \
    --name nginx \
    -v /etc/nginx/conf.d  \
    -v /etc/nginx/vhost.d \
    -v /usr/share/nginx/html \
    -v /tmp/letsencrypt/certs:/etc/nginx/certs:ro \
    xtechnologies/nginx.alpine.arm64v8


docker run -d \
    --name nginx-gen \
    --volumes-from nginx \
    -v /tmp/letsencrypt/nginx.tmpl:/etc/docker-gen/templates/nginx.tmpl:ro \
    -v /var/run/docker.sock:/tmp/docker.sock:ro \
    xtechnologies/docker-gen.alpine.arm64v8 \
    -notify-sighup nginx -watch -only-exposed -wait 5s:30s /etc/docker-gen/templates/nginx.tmpl /etc/nginx/conf.d/default.conf

docker run -d \
    --name letsencrypt \
    -e "NGINX_DOCKER_GEN_CONTAINER=nginx-gen" \
    -e "NGINX_PROXY_CONTAINER=nginx" \
    --volumes-from nginx \
    -v /tmp/letsencrypt/certs:/etc/nginx/certs:rw \
    -v /var/run/docker.sock:/var/run/docker.sock:ro \
    xtechnologies/letsencrypt-companion.arm64v8

docker run -d \
    -e "VIRTUAL_HOST=cloud.gouardes.eu" \
    -e "LETSENCRYPT_HOST=cloud.gouardes.eu" \
    -e "LETSENCRYPT_EMAIL=xavier.gouardes@laposte.net" \
    --name cloud -t xtechnologies/whoami

docker run -d \
    -e "VIRTUAL_HOST=wikixav.gouardes.eu" \
    -e "LETSENCRYPT_HOST=wikixav.gouardes.eu" \
    -e "LETSENCRYPT_EMAIL=xavier.gouardes@laposte.net" \
    --name wikixav -t xtechnologies/whoami

```

## Container nginx sur alpine pour arm64v8
Ressources web : https://github.com/sickp/docker-alpine-nginx.git

Construction de l'image
```
$ docker build -t xtechnologies/nginx.alpine.arm64v8 .
```

Pousser l'image dans le repository
```
docker login -u xtechnologies -p *******
docker push xtechnologies/nginx.alpine.arm64v8
```
## Container docker-gen sur alpine pour arm64v8
Ressources web :
* https://hub.docker.com/r/jwilder/docker-gen/~/dockerfile/
* https://github.com/jwilder/docker-gen

Construction de l'image
```
$ docker build -t xtechnologies/docker-gen.alpine.arm64v8 .
```

### Test de reverse proxy avec deux container whoami
Lancer un container nginx
```
docker run -d -p 80:80 --name nginx -v /tmp/nginx:/etc/nginx/conf.d -t xtechnologies/nginx.alpine.arm64v8
```

Lancer le container docker-gen
```
docker run -d --name nginx-gen --volumes-from nginx \
   -v /var/run/docker.sock:/tmp/docker.sock:ro \
   -v /tmp/templates:/etc/docker-gen/templates \
   -t xtechnologies/docker-gen.alpine.arm64v8 -notify-sighup nginx -watch -only-exposed /etc/docker-gen/templates/nginx.tmpl /etc/nginx/conf.d/default.conf
```

Lancer les deux container whoami
```
docker run -d -e "VIRTUAL_HOST=cloud.gouardes.eu" --name cloud -t xtechnologies/whoami

docker run -d -e "VIRTUAL_HOST=wikixav.gouardes.eu" --name wikixav -t xtechnologies/whoami

```

Pour tester, depuis mon portable
```
$ curl http://cloud.gouardes.eu
I'm 6e0281eff4e8
$ curl http://wikixav.gouardes.eu
I'm 97ebe4961388
```

## todo : faire un docker compose

## Container Who am I
Ressources web :
* https://hub.docker.com/r/jwilder/whoami/
* https://github.com/jwilder/whoami

Construction de l'image
```
$ docker build -t xtechnologies/whoami:1.0.0 .
```

Lancer un container
```
docker run -d -p 8000:8000 --name whoami -t xtechnologies/whoami:1.0.0
```


Pousser l'image dans le repository
```
docker login -u xtechnologies -p *******
docker push xtechnologies/whoami:1.0.0
```

Accéder au container
```
curl http://xodroid:8000
```
