# Traefik docker setup procedure

- clone files in `/opt`
- cd to `traefik` root folder

```
cd /opt/traefik
```

- mkdir `letsencrypt` `gf_data`
- create docker network as `docker network create traefik-public`
- run `docker-compose` up and down

```
docker-compose -f docker-compose-traefik.yml up -d
docker-compose -f docker-compose-apps.yml up -d
```

```
docker-compose -f docker-compose-traefik.yml down
docker-compose -f docker-compose-apps.yml down
```

- open web application url

```
https://grafana.nathansweb.com
https://whoami.nathansweb.com
```

- run

```
chmod 600 /opt/traefik/letsencrypt/acme.json
```

## Miscellaneous commands

- `docker container ls`
- `docker logs [container-name]`
  - traefik
  - whoami
  - grafana
