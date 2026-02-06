
```
ip access-list extended icmp_deny_1./8net

deny icmp 192.0.1.0 0.0.0.255 1.0.0.0 0.255.255.255 echo
permit ip any any

ip access-group icmp_deny_1./8net
```

