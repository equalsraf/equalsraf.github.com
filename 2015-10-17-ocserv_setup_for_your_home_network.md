title: ocserv setup for your home network
hidden:

**EDIT:** This does not work with openconnect *v7.06*, SNI was only
added later.

I'm not using NAT, instead ocserv assigns IP addresses from my local
network i.e. my network assigns address via DHCP in the 192.168.0.10-100 range,
and ocserv assigns addresses in the 192.168.0.200- range. In ocserv docs this
is called the `pseudo-bridge` setup.

Add the following to /etc/sysctl.conf, this will enable IP forwarding
and proxy ARP.

    net.ipv4.conf.eth0.proxy_arp = 1
    net.ipv4.ip_forward = 1

The command `sysctl -p` will reload the settings.

## /etc/ocserv/ocserv.conf

The ocserv manpages do a good job of explaining the setup. Here is a minimal
settings file.

    tcp-port = 4443
    auth = "plain[/etc/ocserv/ocpasswd]" 
    
    server-cert = /etc/ocserv/server-cert.pem
    server-key = /etc/ocserv/server-key.pem
    
    # socket file used for server IPC (worker - sec-mod), will be appended with
    # .PID It must be accessible within the chroot environment (if any), so it is
    # best specified relatively to the chroot directory.
    socket-file = /var/run/ocserv-socket
    
    device = vpns
    
    # Use pseudo bridge with proxy ARP, net/ipv4/conf/eth0/proxy_arp
    ipv4-network = 192.168.0.200/29
    ping-leases = 1

    dns = 192.168.0.1

An important caveat is don't forget to set the **dns** option in your settings, otherwise your clients might use their own DNS servers through the VPN (this was definitely the case with my phone).


## HAProxy

OCServ is ideal because it can run on port 443 and looks like a regular SSL
connection.  But I would also like to run other things on that port, HAProxy is
a common solution for this, e.g. run an https and ssh servers behind port 443. You can do the same for running an HTTPS and OCServ in port 443, here is
an example for SSH+OCServ+HTTP.

    frontend port-443
            bind 0.0.0.0:443
            mode tcp
            tcp-request inspect-delay 5s
            tcp-request content accept if { req.ssl_hello_type 1 }
    
            # SSL connection
            acl proto_tls req.ssl_hello_type 1
            acl ocserv req.ssl_sni -i vpn.mydomain.com
    
            use_backend b_ocserv if ocserv
            use_backend b_https if proto_tls
    
            default_backend b_ssh


The first acl detects a TLS connection. The second detects SSL connections to the `vpn.mydomain.com` hostname, that is how we distinguish OCServ connections from regular HTTPS. If none of them match we assume an SSH connection.

The backend sections are pretty straightforward

    backend b_ssh
            mode tcp
            option tcplog
            server ssh 127.0.0.1:22
            timeout server 2h
    
    backend b_https
            mode tcp
            option tcplog
            server https 127.0.0.1:8443
            timeout server 2h
    
    backend b_ocserv
            mode tcp
            option tcplog
            server https 127.0.0.1:4443
            timeout server 2h


## References

- [Recipes ocserv pseudo bridge](http://www.infradead.org/ocserv/recipes-ocserv-pseudo-bridge.html)
- [co-hosting ocserv and https on the same port](http://lists.infradead.org/pipermail/openconnect-devel/2015-January/002570.html)

