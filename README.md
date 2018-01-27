# letsencrypt
-----
letsencrypt, nginx-proxy pour arm Odroid

Source projet : https://github.com/jwilder?utf8=%E2%9C%93&tab=repositories&q=let&type=&language=

## container Who am I
-----
Ressources web :
* https://hub.docker.com/r/jwilder/whoami/
* https://github.com/jwilder/whoami
* https://hub.docker.com/r/arm64v8/golang/
* https://hub.docker.com/r/arm64v8/alpine/

codeonstruction de l'image
```
$ docker build -t xavier/whoami .
```

Lancer un container
```
docker run -d -p 8000:8000 --name whoami -t xavier/whoami
```

Accéder au container
```
curl http://xodroid:8000
```
