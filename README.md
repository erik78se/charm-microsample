# Overview
This charm deploys a flask application and provides the [interface:http] for other charms to consume it.

This charm deploys a snap with a flask webservice named "microsample". Here is the code for it. [microsample-flask-snap]

The flask app uses the built in development server and is a great starting point for more production grade implementations. 

The snap runs on any linux distribution, which is one of the advantages with snaps. You could consider placing snaps in your IoT devices and deploy them with juju for automated tests and CI/CD pipelines.

This charm is a "hooks only charm" also, so its lightweight in the sense that it doesn't pull in a full python environmet which is the case with reactive based charms. Good to know.

# Stand alone usage
To deploy this charm its as simple as
```
juju deploy cs:~erik-lonroth/microsample
```
Once the service is "Online", you can test accessing the service on default port 8080.
```
curl http://microsample-public-ip:8080/api/info
curl http://microsample-public-ip:8080
```

# Usage with an haproxy for availablity/loadbalancing
A more advanced deployment makes use of [interface:http] of this charm to let other charms that implements it relate to it. 

A real world scenario would be a haproxy placed in front of two or more units of the microsample service to make it high available. This is how you would deploy that.

```
juju deploy cs:~erik-lonroth/microsample
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

# Configuration

Set the "channel" option to change which channel in snapcraft.io should be used to install the [microsample-flask-snap](microsample) snap. Default is "edge" (development)

```juju config microsample channel='stable'```

Set the "port" option to change which port the service will use. Default is "8080".

```juju config microsample port=9090```

# Practice material and tutorials

Consider [tutorial-a-tiny-hooks-charm] to get the hang of hook-only charms. (Beginner level)

## Installing the microsample snap without juju
You can run the snap within this charm without juju by installing it from [snapcraft.io]:
```
sudo snap install --edge microsample 
```
After its installed, you can access it with:
```
curl http://localhost:8080/
```


# Contact Information
Erik Lonroth (erik.lonroth@gmail.com)

[interface:http]: https://discourse.jujucharms.com/t/interface-layers/1121
[microsample-flask-snap]: https://github.com/erik78se/microsample-flask-snap
[tutorial-a-tiny-hooks-charm]: https://discourse.jujucharms.com/t/tutorial-a-tiny-hooks-charm/1315
[snapcraft.io]: https://snapcraft.io/