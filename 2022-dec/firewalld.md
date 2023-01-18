# Firewalld - Using firewall-cmd
firewalld provides a dynamically managed firewall with support for network/firewall zones to define the trust level of network connections or interfaces. It has support for IPv4, IPv6 firewall settings and for ethernet bridges and has a separation of runtime and permanent configuration options. It also supports an interface for services or applications to add firewall rules directly.

## firewall-cmd list of options:
| Options | Descriptions|
---| ---|
| `--get-default-zone` | Print default zone for connections and interfaces. |
| `--get-active-zone` |  Print currently active zones altogether with interfaces and sources used in these zones. Active zones are zones. |
| `--zone=public --list-interfaces` | Print information about the zone zone and its bind interface. |
| `--add-interface=ens33 --zone=public` | Adds an interface to the public zone. |
| `--remove-interface=ens33 --zone=public` |  Removes an interface to the public zone. |
| `--get-services` | To get a list of the supported services. |
| `--list-all` | List everything added for or enabled in all zones. |
| `--list-ports` | it can be either a port and protocol pair or a port range with a protocol. |
| `--add-port=80/tcp` | Add a port number and protocol in pair. |
| `--reload` |  Reload firewall rules and keep state information. |
| `--permanent` | set options permanently and changes are not effective immediately. 
|

`Note: The mot important is reloading the firewalld service`

# Scenario 1:
## A simple firewall rule that allow `https` in the default zone. 
```
firewall-cmd --permanent --zone=public --add-service=https
```


# Scenario 2:
## Using zones and sources to allow a service for only a specific domain

To allow traffic from a specific network to use a service on a machine, use `trusted zones` and `source`. The following procedure allows only HTTP traffic from the 103.21.244.0/22 network while any other traffic is blocked.

`Warning:
When you configure this scenario, use the `trusted`  zone. Although  `public`  zone is still the active default zone. Also, be sure to remove the  `http`  service in the  `public`  zone.`

## Procedure:

List all available zones:

```
firewall-cmd --get-zones
block dmz drop external home internal public trusted work
```

`Note: If you want to reset all your firewall configuration, just remove the zone directory, it will be re-created after you run the firewall-cmd command again.`
 ```
sudo rm -r /etc/firewalld/zones/
 ```   
Add the IP range to the internal/public zone to route the traffic originating from the source through the `trusted zone`:
```
firewall-cmd --zone=trusted --add-source=103.21.244.0/22
```
Add the http service to the public zone:
```
firewall-cmd --zone=trusted --add-service=http
```

Make the new settings persistent:
```
firewall-cmd --runtime-to-permanent
```
Reload the firewall configuration:
```
firewall-cmd --reload
```
This will make the `trusted zone`  active as well.

## Verification

Check that the internal zone is active and that the service is allowed in it:
```
firewall-cmd --zone=trusted --list-all
    trusted (active)
      target: default
      icmp-block-inversion: no
      interfaces:
      sources: 103.21.244.0/22
      services: http
```
[RedHat Documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html-single/configuring_firewalls_and_packet_filters/index)


## Additional Resource for cloudFlare list of trusted IP Sources:
                    ALLOW IN    103.21.244.0/22
                    ALLOW IN    103.22.200.0/22
                    ALLOW IN    103.31.4.0/22
                    ALLOW IN    104.16.0.0/12
                    ALLOW IN    108.162.192.0/18
                    ALLOW IN    131.0.72.0/22
                    ALLOW IN    141.101.64.0/18
                    ALLOW IN    162.158.0.0/15
                    ALLOW IN    172.64.0.0/13
                    ALLOW IN    173.245.48.0/20
                    ALLOW IN    188.114.96.0/20
                    ALLOW IN    190.93.240.0/20
                    ALLOW IN    197.234.240.0/22
                    ALLOW IN    198.41.128.0/17