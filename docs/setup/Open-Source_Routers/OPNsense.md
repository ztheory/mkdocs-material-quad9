### Introduction

OPNsense is an open-source firewall, used in both consumer and commercial environments.

OPNsense utilizes Unbound, which has built-in DNS over TLS support, with the configuration being accessible in the GUI.

Before making changes to a production environment, we recommend taking a backup of the existing configuration.

### Steps

Step 1

* Navigate to Services -> DNS over TLS on the left-side menu
*  Click the + button

Add 4 entries, using `dns.quad9.net` in the **Verify CN** Field

Server IP: 9.9.9.9
Server Port: 853

Server IP: 149.112.112.112
Server Port: 853

Server IP: 2620:fe::fe
Server Port: 853

Server IP: 2620:fe::9
Server Port: 853

If your network does not have IPv6, which you can test here, then IPv6 addresses should not be added, as it may result in a percentage of your DNS requests failing.

Click on Apply to save the changes.

Step 2
Navigate to System -> Settings -> General on the left-side menu.

* Disable Allow DNS server list to be overridden by DHCP/PPP on WAN
* Click Save
* Click Apply at the top of the page

Step 3

To can confirm that OPNsense is now sending your queries via DNS over TLS, you can run a packet capture in command line, such as:

```
tcpdump -i em0 'port 853'
```

You may have to adjust the interface name from em0 to that of your device's WAN interface.

You can also run a test from a macOS, Linux, or Windows system on the network.
