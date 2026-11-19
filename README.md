# Description

Installs NetBird from its official APT repository and enrolls host as a routing peer. Enables persistent IPv4 and IPv6 forwarding.

# Configuration

Provide a setup key, preferably through Ansible Vault:
```yaml
netbird_setup_key: "{{ vault_netbird_setup_key }}"
```
The role uses the key once and writes `/var/lib/netbird/ansible-setup-complete` to avoid reusing one-time keys.
To enroll again, remove this marker and provide a valid setup key.

# Management

Check the service and peer status with:

```sh
systemctl status netbird
netbird status
netbird networks ls
```

# Known Issues

The routing peer needs network reachability to destination hosts. If masquerading is disabled in the dashboard, configure return routes to the NetBird network on the destination network.
