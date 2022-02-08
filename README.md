# Overview
This charm deploys a flask application and provides the [interface:http] for other charms to relate to it.

The deployed snap is a flask webservice named [microsample]. Here is the code for it. [microsample-flask-snap]. The webservice is the development flask web-server and is a starting point for more production grade implementations.

The snap runs on any linux distribution, which is one of the advantages with snaps. You could consider placing snaps in your IoT devices and deploy them with juju for automated tests and CI/CD pipelines.

This charm is a "hooks only charm" (Learn about it in the [tutorial-a-tiny-hooks-charm]). The charm is lightweight in the sense that it doesn't pull in a full python environmet which is the case with reactive based charms.

# Stand alone usage
To deploy this charm its as simple as
```
juju deploy microsample
juju expose microsample
```
Once the service is "Online", you can test accessing the service on default port 8080.
```
curl http://microsample-public-ip:8080/api/info
curl http://microsample-public-ip:8080
```

## A note on Google Cloud
There is a bug in juju [https://bugs.launchpad.net/juju/+bug/1761838](1761838) which returns the fan network address for a private address. 
This breaks the deploy at least on juju2.8. The workaround for this is to remove the fan networking:
May have been fixed with juju 2.9
```
juju model-config fan-config="" container-networking-method=""
```


# Usage with an haproxy for availablity/loadbalancing
A more advanced deployment makes use of the [interface:http] of this charm.
By relating the microsample charm with [haproxy] and adding a second unit of microsample, microsample becomes high available.

![HA Deployment](https://raw.githubusercontent.com/erik78se/charm-microsample/master/microsample-ha.png)


```
juju deploy microsample
juju add-unit microsample
juju deploy haproxy
juju relate microsample:website haproxy:reverseproxy
juju expose haproxy
```
Now, access the service on port 80 as:
```
curl http://haproxy-public-ip/api/info
curl http://haproxy-public-ip
```

You can verify the high availability by shuting down one of the units and you should still get a reply. 




# Practice material and tutorials

Consider [tutorial-a-tiny-hooks-charm] to get the hang of hook-only charms. (Beginner level)

Studying the code for [microsample-flask-snap] is a great excersise to learn snap/snapping.

## Installing the microsample snap without juju
You can run the [microsample] snap within this charm without juju by installing it from [snapcraft.io]:
```
sudo snap install --edge microsample 
```
After its installed, you can access it with:
```
curl http://localhost:8080/
```

You can even put a haproxy with ssl-termination in front of it by generating a ssl-cert,
and then do a

    juju config haproxy ssl_cert="$(cat server.pem)"
    juju config haproxy ssl_key="$(cat server.key)"

... you can tweak haproxy aswell by injecting your own haproxy config as [services.yaml](services.yaml)

    juju config haproxy services="$(cat services.yaml)"
    
    curl -k https://<yourhostname>
    Online

# Contact Information
Erik Lonroth (erik.lonroth@gmail.com)

[interface:http]: https://discourse.jujucharms.com/t/interface-layers/1121
[microsample-flask-snap]: https://github.com/erik78se/microsample-flask-snap
[tutorial-a-tiny-hooks-charm]: https://discourse.jujucharms.com/t/tutorial-a-tiny-hooks-charm/1315
[snapcraft.io]: https://snapcraft.io/
[ssl-termination-proxy]: https://jujucharms.com/ssl-termination-proxy
[haproxy]: https://jujucharms.com/haproxy/
[microsample]: https://snapcraft.io/microsample
