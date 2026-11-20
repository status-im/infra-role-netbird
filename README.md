# Description

Installs NetBird from its official APT repository and enrolls host as a routing peer. Persistent IPv4 and IPv6 forwarding can be enabled with an nftables masquerade rule for configured destination networks.

# Configuration

Provide a setup key, preferably through Ansible Vault:
```yaml
netbird_setup_key: "{{ vault_netbird_setup_key }}"
netbird_forwarding_enabled: true
netbird_forwarding_networks:
  - '10.10.0.0/16'
  - '10.11.0.0/16'
```
The role uses the key once and writes `/var/lib/netbird/ansible-setup-complete` to avoid reusing one-time keys.
To enroll again, remove this marker and provide a valid setup key.

Forwarding defaults to enabled, matching the role's previous behavior. When enabled, `netbird_forwarding_networks` must contain one or more destination CIDRs. The role writes `/etc/nft.conf.d/postrouting/netbird.nft` and reloads the nftables service when that file changes; source network (`100.78.0.0/16`) and interfaces (`wt0` to `wg0`) are configurable with `netbird_forwarding_source_network`, `netbird_forwarding_in_interface`, and `netbird_forwarding_out_interface`. Set `netbird_forwarding_enabled: false` to disable IPv4/IPv6 forwarding and remove the managed rule file.

The postrouting masquerade rule NATs matching destinations; it does not block forwarding to destinations outside the list. Enforce a separate nftables forward-chain policy if non-listed networks must be denied.

# Management

Check the service and peer status with:

```sh
systemctl status netbird
netbird status
netbird networks ls
```

# Known Issues

The routing peer needs network reachability to destination hosts. If masquerading is disabled in the dashboard, configure return routes to the NetBird network on the destination network.
