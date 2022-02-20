# Traefik docker setup procedure

- clone the files
- cd to `traefik` root folder
- mkdir `letsencrypt` `gf_data`
- create docker network as `docker network create traefik-public`
- run

```
docker-compose -f docker-compose-traefik.yml up -d
docker-compose -f docker-compose-apps.yml up -d
```

- open web application url

```
https://grafana.nathansweb.com
https://whoami.nathansweb.com
```
