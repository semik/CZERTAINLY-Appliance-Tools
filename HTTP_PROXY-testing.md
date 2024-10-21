# Testing HTTP_PROXY with Virtualbox

## Installing Squid on Debian GNU/Linux

I'm running Virtualbox on my workstation=lab.

So I installed Squid on my workstation, config for `vboxnet0` interface:

```vboxnet0.conf 
acl purge method PURGE
acl CONNECT method CONNECT

acl vboxnet0_network src 192.168.56.0/24
http_access allow vboxnet0_network

# Docker registry-specific refresh patterns
refresh_pattern -i \.docker\.com/.* 43200 100% 43200
refresh_pattern -i registry-1\.docker\.io/.* 43200 100% 43200
refresh_pattern -i cloudfront\.net/.* 43200 100% 43200
refresh_pattern -i .* 0 20% 43200

# Cache control for Docker layers (large files)
range_offset_limit -1
maximum_object_size 2 GB

# Deny all other access
http_access deny all
```

## Conducting tests

Import virtual appliance. Before executing it, modify network settings from briged mode to Host-only - this is easy way how to cut appliance from lab network. But to get Ansible playbooks working it is necessary to manualy setup gateway:
```bash
$ sudo ip route add default via 192.168.56.1 dev eth0
$ ip route
default via 192.168.56.1 dev eth0 
192.168.56.0/24 dev eth0 proto kernel scope link src 192.168.56.6
```
make sure there is **default** route present, otherwise Ansible will be unable to identify primary IP adress and installation process will fail.

Configure HTTP proxy settings in *main menu -> network -> proxyHTTP*, typicaly same URL into all four input fields http://192.168.1.1:3128 and after that restart SSH session to the Appliance to apply environemnt settings! Dialog will remand you this.

Conduct tests.
