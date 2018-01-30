# letsencrypt
-----
letsencrypt, nginx-proxy pour arm Odroid

Source projet : https://github.com/jwilder?utf8=%E2%9C%93&tab=repositories&q=let&type=&language=

## Container nginx sur alpine pour arm64v8
-----
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

## Container nginx-proxy
-----
Ressources web :

Sous-domaines
```
wikixav.gouardes.eu
cloud.gouardes.eu
```

## Container Who am I
-----
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
