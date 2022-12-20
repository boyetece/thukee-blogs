# Redhat 9.++ Configuring network bonding

A network bond is a method to combine or aggregate physical and virtual network interfaces to provide a logical interface with higher throughput or redundancy. In a bond, the kernel handles all operations exclusively. You can create bonds on different types of devices, such as Ethernet devices or VLANs. In this case we will use `nmcli` command to accomplish that.

This is a method to combine or aggregate network interfaces to provide a logical interface with higher throughput or redundancy. And we will use `mode 4`, the Link Aggregation Control Protocol (LACP) and EtherChannel on the manage switch. These are the list of mode available:

|Bonding mode |Configuration on the switch  |
--- | --- |
| 0 - balance-rr |Requires static Etherchannel enabled (not LACP-negotiated)|
| 1 - active-backup | Requires autonomous ports |
| 2 - balance-xor | Requires static Etherchannel enabled (not LACP-negotiated) |
| 3 - broadcast | Requires static Etherchannel enabled (not LACP-negotiated) |
| `4 - 802.3ad`  | Requires LACP-negotiated Etherchannel enabled  |
| 5 - balance-tlb  | Requires autonomous ports  |
| 6 - balance-alb  | Requires autonomous ports  |


## Procedure
1. We will create abond interface `bond0` using `mode 4`

    ```
    ~$ nmcli connection add type bond con-name bond0 ifname bond0 bond.options "mode=802.3ad"
    ```

1. Checking on the interfaces available to add to the bond

    ```
    ~$ nmcli device status
        DEVICE   TYPE      STATE         CONNECTION
        enp2s0   ethernet  disconnected  --
        enp3s0   ethernet  disconnected  --
        bridge0  bridge    connected     bridge0
        bridge1  bridge    connected     bridge1
    ```
1. Assign interfaces to the bond

    ```
    ~$ nmcli connection add type ethernet slave-type bond con-name bond0-port1 ifname enp2s0 master bond0

    ~$ nmcli connection add type ethernet slave-type bond con-name bond0-port2 ifname enp3s0 master bond0
    ```
1. Configure an  IP address for the bond0 interface

    ```
    ~$ nmcli connection modify bond0 ipv4.addresses '192.168.100.10/24'
    ~$ nmcli connection modify bond0 ipv4.gateway '192.168.100.254'
    ~$ nmcli connection modify bond0 ipv4.dns '192.168.100.3'
    ~$ nmcli connection modify bond0 ipv4.dns-search 'cloudflare.com'
    ~$ nmcli connection modify bond0 ipv4.method manual
    ```
1. Activate the connection

    ```
    ~$ nmcli connection up bond0
    ```
1. Verify that the ports are connected

    ```
    ~$ nmcli device
        DEVICE   TYPE      STATE      CONNECTION
        enp2s0   ethernet  connected  bond0-port1
        enp3s0   ethernet  connected  bond0-port2
    ```
    Note: When you activate any port of the connection, NetworkManager also     activates the bond, but not the other ports of it. You can configure that Red Hat Enterprise Linux enables all ports automatically when when the bond is enabled.
    
1. Enable and reactivate bond0

    ```
    ~$ nmcli connection modify bond0 connection.autoconnect-slaves 1
    ```
    ```
    ~$ nmcli connection up bond0
    ```

1. Display the status of bond0

    ```
    ~$ cat /proc/net/bonding/bond0
    ```



Reference:
[Redhat 9.++ documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/configuring_and_managing_networking/configuring-network-bonding_configuring-and-managing-networking)
