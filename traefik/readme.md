# Traefik docker setup

## Pre-requisite

- Valid domain name
- aws credentials i.e. access and secret access key
- Python3

## Setup procedure

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

## PynamicDNS setup

PynamicDNS - Dynamic DNS With Raspberry Pi, Python and AWS

```
python3 ~/PynamicDNS/PynamicDNS.py <DNS Record> <Hosted Zone ID>

e.g.
python3 ~/PynamicDNS/PynamicDNS.py grafana.nathansweb.com Z1QPPN7T5EUAF7
```

- [PynamicDNS](https://tynick.com/blog/03-16-2020/pynamicdns-dynamic-dns-with-raspberry-pi-python-and-aws/)

## Miscellaneous commands

- `docker container ls`
- `docker logs [container-name]`
  - traefik
  - whoami
  - grafana
