# How to create Fake DNS in Mac OS for Local Kubernetes Ingress testing

Often times we need a way to simulate local domain name pointing to your local `api` or `web` development and it is critical if you use kubernetes ingress controller or traefik proxy to test your configuration in yoou local Mac laptop.

> create a new ip
```
sudo ifconfig lo0 10.0.0.1 alias
```

> Tunnel or forward `10.0.0.1:80` to custom port to your localhost. In this case, post `3000`

```
echo "rdr pass on lo0 inet proto tcp from any to 10.0.0.1 port 80 -> 127.0.0.1 port 3000" | sudo pfctl -ef -
```

> Then edit your /etc/hosts or /private/etc/hosts file and add the following line to map your domain `zypress.me` to `10.0.0.1`

```
10.0.0.1 zypress.me
```

> After you save your hosts file flush your local DNS. See references section below
```
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder
```

> Access application using the local fake dns `http://zypress.me`

How to Delete the just created ip?
```
sudo ifconfig lo0 10.0.0.1 -alias
```


## References
* [Flushing your DNS cache in Mac OS X and Linux](https://help.dreamhost.com/hc/en-us/articles/214981288-Flushing-your-DNS-cache-in-Mac-OS-X-and-Linux)
* [Stackoverflow](https://serverfault.com/questions/102416/iptables-equivalent-for-mac-os-x/673551#673551)