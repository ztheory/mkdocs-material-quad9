## Introduction

You're administrating DNS for a building, office, business, ISP, etc, and you want to use Quad 9. Great choice!

Caching forwarders and their optimal configuration are critical when sending queries en masse to Quad9, and is highly preferred over direct assignment via DHCP to end users with regards to:

Performance - Reducing the amount of queries that recurse to Quad9, saving bandwidth and providing a faster experience for the end user when their queries are already in the forwarders' cache.
Security - Enabling logging is advised to identify possible compromise from specific endpoints or customers, and is sometimes required by local law.
Local Policy - Being able to block or analyze certain FQDNs at the forwarder level puts more control in the hands of the network administrator without relying exclusively on Quad9 to block malicious domains.

When setting Quad9 as the recursive resolver in your infrastructure and caching DNS forwarders, such as BIND, Windows DNS Server, Knot Resolver, dnsdist, Unbound, etc, please consider the following best practices.

## Volume

For ISPs or organizations with more than 10,000 users behind a forwarding cache, or if you expect more than 500 queries per second from a single egress IP address, please contact Quad9 Support with the details of your deployment, so that we can work together to ensure a smooth and successful deployment.

## Exclusivity

Since DNS forwarders use round-robin ordering when forwarding queries to a list of recursive DNS servers, Quad9 must be set as the exclusive recursive DNS servers in your forwarders. Adding additional, non-Quad9 recursive DNS servers will result in a percentage of your DNS queries not being protected by Quad9's threat blocking.

## Caching

It is imperative that your DNS forwarders are configured to cache response data in order to avoid excessive recursive queries to Quad9 and to provide significantly faster DNS resolution for devices on the network.

Ensure that your DNS forwarders have enough memory or disk space allocated to the cache to avoid the cache filling up.

The amount of memory that should be dedicated to DNS caching varies greatly from megabytes to gigabytes based on the amount of DNS requests originating from your network endpoints.

### dnsdist

dndsdist is a highly DNS-, DoS- and abuse-aware loadbalancer with impressive performance and is well suited as a caching and load balancing DNS forwarder.

Caching is disabled by default, but can be enabled for in-memory storage.


### Unbound

Allocated cache size is determined by the msg-cache-size and rrset-cache-size options in the unbound.conf file.

You can check the amount of memory that your cache is currently using to compare against the cache size you allocated in unbound.conf by using the unbound-control command to view stats for mem.cache.rrset and mem.cache.message values.

Unbound can also be configured to use Redis in order to share a common cache between multiple DNS forwarders. Refer to the Cache DB Module Options in the unbound.conf documentation.

### Knot Resolver

Knot Resolver caches on disk by default, but can be configured to use memory/tmpfs, backends, and share cache between instances. Knot Resolver has excellent documentation about all things caching.

### BIND

Bind caches in memory by default, so the only limitation is exhausting available memory in the system.

To check the size of the current cache, you can dump the cache to a local file and then examine the file size, which will be approximately how much memory is being used by cache:

```
rndc dumpdb -all
```

```
ls -alh /var/bind/
```

### Windows DNS Server

In-memory caching can be configured using the Set-DnsServerCache cmd applet.

Memory usage can be checked using the `Get-DnsServerStatistics` cmd applet.

## Use the Primary and Secondary Quad9 IP Addresses

Configuring both the primary and secondary IP of your desired Quad9 service helps naturally load balance the DNS queries in the Quad9 infrastructure.

## Use IPv6

If your network is capable of IPv6, also configure the primary and secondary IPv6 addresses of your desired Quad9 service in your DNS forwarders, which helps naturally load balance the DNS queries in the Quad9 infrastructure.

If IPv6 is not in use, Quad9 strongly encourages you to investigate how to get it enabled on your network. IPv6 route paths are often faster compared to IPv4 paths, which leads to a higher chance of success at faster speeds with better redundancy.

## Separate IP Addresses for Each DNS Forwarder

Each DNS forwarder should, ideally, send and receive DNS queries to Quad9 using different public IPv4 and IPv6 addresses, even if the addresses are within the same subnet.

## Disable DNSSEC Validation

Since Quad9 already performs DNSSEC validation, DNSSEC being enabled in the forwarder will cause a duplication of the DNSSEC process, significantly reducing performance and potentially causing false BOGUS responses.
