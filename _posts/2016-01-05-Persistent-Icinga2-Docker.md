---
title: Persistent Icinga2 Docker on RaspberryPi Cluster
published: true
layout: post
disqus: yes
fbcomments: no
category: monitoring 
description: Icinga2 is a next gen fork of Nagios, to continue playing with it you might want a little bit of persistence
tags: 
  - linux
  - icinga
photo_url: /img/thumbs/icinga_logo.jpg
---

![](/img/icinga_logo.jpg)

[Icinga2](https://www.icinga.org/icinga/icinga-2/) is a next gen fork of [Nagios](https://www.nagios.com/).
While using the open source base from the organization [Nagios](https://www.nagios.org/) you always felt like you were missing out
by not paying your dues to Nagios Enterprise. Along came Icinga, a fork with much of the features offered by the enterprise version of nagios 
for free and much more.  It was open source and all the plugins still worked.  So repositories of generic plugins like this [one](https://www.monitoring-plugins.org/)
did not go to waste.  But with the second version they really upped the ante, the full feature list is [here](https://www.icinga.org/icinga/icinga-2/features/).  
Some of the most impressive improvements are in the clustering area.  Well that makes it a great candidate for dockerization.

Earlier we used ansible to spin up a docker container I have a previous [post](http://joshuacox.github.io/docker/2015/11/28/Icinga2-Docker/) 
on this topic, but I felt the other methods were failing me and I wanted more persistence

I started trying to convert the official container into a persistent instance with [mkicinga](https://github.com/joshuacox/mkicinga), 
however, mysql was acting funny on me, and I’d rather not have it in the image anyhow, so I went back to the fork of jeyk’s old debian based [Dockerfile](http://joshuacox.github.io/docker-icinga2)
I peeled mysql out into it's own conatiner and then fiddled with the initdocker script from the official repo until it started working in debian

#### Requirements

* [RaspberryPi Full kit](http://astore.amazon.com/joshuacox-20/detail/B00MV6TAJI/189-3117811-4891539) at least one, but I’d recommend three
* [Cluster setup](http://joshuacox.github.io/docker/2015/12/13/RaspberryPi-Docker-Cluster-Consul-Swarm/) from our previous post there

#### Usage

should be easy first pull the temporary recipe up

```
make temp
```

Let it finish populating the databases, (you can watch by using `make logs` ctrl-c to exit viewing the logs, despite what the warning says you will actually not kill the container
[because your are killing the `tail -f` of the log watching process not the process itself in this case])
and `killall mysql`


```
make enter      # we have ‘entered’ the container
exit            # back in the host environment now
```


then grab all the persistent volumes

```
make grab
```

then run in `prod` mode, take this ‘prod’ with a grain of salt, I’ll leave it to you to understand all security implications of the 
MYSQL_ROOT_PASS file hanging out after these Makefiles have ran (i.e. on all docker machines you should implicitly trust all users with the ‘docker’ group as they effectively have root on the machine)

```
make prod
```

### Icinga Web 2

Icinga Web 2 can be accessed at http://localhost:3080/icingaweb2 w/ `icingaadmin:icinga` as credentials.

The configuration is located in /etc/icingaweb2 which is exposed as [volume](#volumes) from
docker.

By default the icingaweb2 database is created including the `icingaadmin` user. Additional
configuration is also added to skip the setup wizard.

## Ports

The following ports are exposed:

  Port     | Service
  ---------|---------
  22       | SSH
  80       | HTTP
  443      | HTTPS
  3306     | MySQL
  5665     | Icinga 2 API & Cluster

## Volumes

These volumes can be mounted in order to test and develop various stuff.

    /etc/icinga2
    /etc/icingaweb2

    /var/lib/icinga2
    /usr/share/icingaweb2

    /var/lib/mysql

#### Clustering 

#####  master setup

Now let's set it up our master, enter our 

```
make enter      # we have ‘entered’ the container
icinga2 node wizard
```

you'll answer 'n' to the first question as this is the master, when it asks you about bind hosts and ports just hit return the defaults are fine here.

##### client setup

Now setup a seond host with icinga2 on it, you can use our docker container again if you like.
On this second host go though the wizard again, this time answer 'Y' to the first question as this is a satellite node

```
icinga2 node wizard
```

you will be prompted for a pki key at one point. It provides a hint, something similar to:

```
icinga2 pki ticket --cn $HOSTNAME
```

you need to execute this on the master, it will output a key that you need to then paste into the prompt from the wizard back on the satellite node.  
This will setup encrypted communication between the master and satellite node.

Now it's time to restart the nodes, if using my docker container just use the prod recipe again

```
make prod
```

###### Discovery of the new nodes and services

To see your new node log back into the master and issue this command:

```
icinga2 node list
```

And then to add the configs incant this:

```
icinga2 node update-config
```

restart the nodes and you should have your new host auto discovered and monitored

For more details view the full [Icinga2 client docs](http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/icinga2-client)

#### Monitoring Plugins

You might notice some plugins missing in the form of error messages of icinga searching for stuff in `/usr/lib64/nagios/plugins`

CentOS and RHEL systems in particular only come with a dismal amount of plugins, so let's install the gold standard in terms of plugins
download [here](https://www.monitoring-plugins.org/download.html), and then uncompress and install them

```
gzip -dc monitoring-plugins-2.x.tar.gz | tar -xf -
cd monitoring-plugins-2.x
./configure
make
make install
```

Then change the `PluginDir` line in  `/etc/icinga2/constants.conf`

```
const PluginDir = "/usr/local/nagios/plugins"
```

### Distributed Monitoring and High Availability

Now to configure everything you'll need to add each endpoint and zone as described here in the [Icinga2 docs](http://docs.icinga.org/icinga2/snapshot/doc/module/icinga2/chapter/distributed-monitoring-high-availability)


again main page here for my docker-icinga2:

[joshuacox.github.io/docker-icinga2](http://joshuacox.github.io/docker-icinga2)

For more related errata visit my blog [joshuacox.github.io](http://joshuacox.github.io/)
