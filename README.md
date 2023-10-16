# ACL

Access Control Lists (ACLs) in Cisco devices are used to filter network traffic based on various criteria. This can be used for security purposes, traffic management, or to implement certain network policies.

## Structure Commands

here are the structures for the command line configurations of the three types of Access Control Lists (ACLs) on Cisco routers and switches:

Standard ACL:
```
Router(config)# access-list <access-list-number> {deny | permit} <source-address> <wildcard-mask>
    <access-list-number>: This is a number between 1 and 99. The number indicates that it's a standard ACL.
    {deny | permit}: This is the action to take - either deny or permit the traffic.
    <source-address>: This is the IP address of the source of the traffic.
    <wildcard-mask>: This is the wildcard mask for the source. It's the opposite of a subnet mask.
```
Extended ACL:
```
Router(config)# access-list <access-list-number> {deny | permit} <protocol> <source-address> <wildcard-mask> <destination-address> <wildcard-mask> [eq <port-number>]

    <access-list-number>: This is a number between 100 and 199. The number indicates that it's an extended ACL.
    <protocol>: This is the protocol of the traffic, such as TCP, UDP, or ICMP.
    <destination-address> and <wildcard-mask>: These are the IP address and wildcard mask of the destination of the traffic.
    [eq <port-number>]: This is an optional part of the command. If included, it specifies the port number for the traffic.
```
Named ACL (Standard and Extended):
```
Router(config)# ip access-list {standard | extended} <acl-name>
Router(config-ext/std-nacl)# {deny | permit} ...

    <acl-name>: This is the name of the ACL. It can be any alphanumeric string.
    The rest of the command is the same as a standard or extended ACL, depending on which type you're creating.
```

There are three main types of ACLs: Standard, Extended, and Named.

1. Standard ACLs:

Standard ACLs are the simplest form of ACLs. They only examine the source IP address of the packet to decide whether to permit or deny the traffic.

Example 1:
Example of a Standard ACL:
```
Router(config)# access-list 10 permit 192.168.1.0 0.0.0.255
Router(config)# access-list 10 deny any
```
In this example, the ACL with ID 10 permits all traffic from the 192.168.1.0/24 network, and denies all other traffic. Remember, ACLs have an implicit "deny any" at the end, so the second line isn't strictly necessary but is often included for clarity.

Example 2:
Let's imagine you want to block traffic from a specific host (192.168.1.10) while permitting all other traffic.
```
Router(config)# access-list 10 deny host 192.168.1.10
Router(config)# access-list 10 permit any
```
In this example, ACL 10 blocks traffic from the host at 192.168.1.10 and allows all other traffic.

2. Extended ACLs:

Extended ACLs are more complex and powerful. They can examine both source and destination IP addresses, and can also filter based on the protocol (TCP, UDP, ICMP, etc.) and ports.

Example 1:
Example of an Extended ACL:
```
Router(config)# access-list 101 permit tcp 192.168.1.0 0.0.0.255 192.168.2.0 0.0.0.255 eq 80
Router(config)# access-list 101 deny ip any any
```
In this example, the ACL with ID 101 permits TCP traffic from the 192.168.1.0/24 network to the 192.168.2.0/24 network on port 80 (typically used for HTTP). All other IP traffic is denied.

Example 2:
Suppose you want to allow only HTTPS (port 443) and SSH (port 22) traffic from network 192.168.1.0/24 to a specific server (192.168.2.5), and deny all other traffic.
```
Router(config)# access-list 101 permit tcp 192.168.1.0 0.0.0.255 host 192.168.2.5 eq 443
Router(config)# access-list 101 permit tcp 192.168.1.0 0.0.0.255 host 192.168.2.5 eq 22
Router(config)# access-list 101 deny ip any any
```
This ACL allows only HTTPS and SSH traffic from the 192.168.1.0/24 network to the 192.168.2.5 server, and blocks all other IP traffic.

3. Named ACLs:

Named ACLs are similar to numbered ACLs, but they use names instead of numbers, which can make them easier to work with, especially in larger configurations.

Example of a Named ACL:

Example 1:
```
Router(config)# ip access-list extended WEB_TRAFFIC
Router(config-ext-nacl)# permit tcp 192.168.1.0 0.0.0.255 192.168.2.0 0.0.0.255 eq 80
Router(config-ext-nacl)# deny ip any any
```
This named ACL 'WEB_TRAFFIC' does exactly the same thing as the extended ACL in the previous example.

Example 2:
Now let's say you want to permit ICMP traffic (pings) from a specific network (192.168.1.0/24) to any destination, and deny all other traffic. We'll do this with a named ACL this time.
```
Router(config)# ip access-list extended ALLOW_PING
Router(config-ext-nacl)# permit icmp 192.168.1.0 0.0.0.255 any
Router(config-ext-nacl)# deny ip any any
```
This ACL, named 'ALLOW_PING', permits ICMP traffic from the 192.168.1.0/24 network to any destination, and denies all other IP traffic

Example 3:
Let's say you want to allow ICMP echo replies (ping replies) from any host to network 192.168.1.0/24, and deny all other ICMP traffic.
```
Router(config)# ip access-list extended ALLOW_PING_REPLY
Router(config-ext-nacl)# permit icmp any 192.168.1.0 0.0.0.255 echo-reply
Router(config-ext-nacl)# deny icmp any any
```
This ACL, named 'ALLOW_PING_REPLY', allows ICMP echo replies from any host to the 192.168.1.0/24 network, and denies all other ICMP traffic.


Best Practices for ACLs:

1. Plan your ACLs: Before implementing any ACL, have a clear understanding of the traffic flow in your network and what exactly you want to allow or deny.
2. Be specific: Use the most specific criteria to match traffic. This prevents unintended traffic from being matched.
3. Order matters: The order of rules in an ACL is important because packets are evaluated against the rules in the order they appear and the process stops at the first match. Place the more specific rules at the top of the ACL.
4. Use remarks: In complex ACLs, use the 'remark' command to add comments. This can help you and others understand what each entry in the ACL is for.
5. Test before implementing: Always test your ACLs in a lab environment before deploying them in production. A small mistake in an ACL can lead to a network outage.
6. Use logging: Use the 'log' keyword at the end of ACL entries to generate log messages when the rule is matched. This can be useful for troubleshooting.
