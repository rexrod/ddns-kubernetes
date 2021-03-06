# ddns-kubernetes

This is a configuration for dynamic DNS on a local network integrated with Kubernetes and made with Vagrant.

## Hosts Configurations

|Host 	|Função 	                |FQDN privado 	        |Endereço IP privado
|-      |-                          |-                      |-
|ns1 	|Servidor DNS primário 	    |ns1.itbam.io 	        |10.128.10.11
|ns2 	|Servidor DNS secundário    |ns2.itbam.io 	        |10.128.20.12
|host1 	|Host genérico 1 	        |host1.itbam.io 	    |10.128.100.101
|host2 	|Host genérico 2 	        |host2.itbam.io 	    |10.128.200.102

## Dependecies

* Vagrant 2.2.9
* VirtualBox 5.2 or superior

## Run infrastructure

```
vagrant up
```

## Tests

Acess `host1` via ssh:

```
vagrant ssh host1
```

Checks if the `host2` is accessible:

```
ping host2
```

More details:

```
nslookup host2
```

Reverse zone:

```
nslookup 10.128.200.102
```

Verify domain configuration:

```
systemd-resolve --status
```

> You can do the same tests on `host1`

## Add Host

Add host, example:

```
vagrant ssh ns1 -c "sudo ns-hostctl host3 10.128.200.103 itbam.io"
vagrant ssh ns2 -c "sudo ns-hostctl host3 10.128.200.103"
```

> If no error is returned, it means that the configuration was successful.

### Possible problems after adding the host

If the `ping` or `nslookup` command is executed before the host exists, create a kind of cache in which it has the two problems shown below

<pre>
vagrant@host2:~$ ping host7 
ping: host7: Temporary failure in name resolution 
</pre>

<pre>
vagrant@host2:~$ nslookup host7
Server:         127.0.0.53
Address:        127.0.0.53#53 

server can't find host7: SERVFAIL
</pre>

To solve this problem, you must reload the network settings with the following command on the host where Ping or Nslookup was performed:

```
sudo netplan apply
```

## Clean Boxes

```
vagrant destroy -f
```

> based on: https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04-pt
