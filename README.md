# letsencrypt
letsencrypt, nginx-proxy pour arm Odroid

Source projet : https://github.com/jwilder?utf8=%E2%9C%93&tab=repositories&q=let&type=&language=

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
