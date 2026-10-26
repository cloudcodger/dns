# The knot_resolver role

Set up a debian based system as an Name Server Resolver using `knot-resolver`.

This is not an authoritative DNS server and is meant to work in conjunction with the `knot_auth` role.

# Variables

## Required variables.

Although these have default values, the defaults are not usable. See the descriptions for them below.

- `knot_resolver_internal_domains`
- `knot_resolver_stub_servers` when using a different authoritative name servers

## All Variables.

- `knot_resolver_cache_size_mb`
  - The size of the resolver cache.
  - Default: `200`

- `knot_resolver_instances`
  - How many resolver instances to run.
  - Default: `2`

- `knot_resolver_internal_domains`
  - A list of domain names that are internal and stubbed to the `knot_resolver_lan_ip`.
  - Default: `[]`

- `knot_resolver_lan_ip`
  - The IP address of the Knot Authoritative Name Server.
  - Default: `"{{ ansible_facts['default_ipv4']['address'] }}"`

- `knot_resolver_listeners`
  - A list of dicts of `addr`, `port`, and `kind` for listeners.
  - Default:
      - {addr: `"127.0.0.1"`, port: `53`, kind: `"dns"`}
      - {addr: `"{{ knot_resolver_lan_ip }}"`, port: `53`, kind: `"dns"`}
      - {addr: `"127.0.0.1"`, port: `853`, kind: `"tls"`}
      - {addr: `"{{ knot_resolver_lan_ip }}"`, port: `853`, kind: `"tls"`}

- `knot_resolver_rpz_dir`
  - The directory for the RPZ files.
  - Default: `/etc/knot-resolver`

- `knot_resolver_rpz_allowlist_domains`
  - A list of domains to place in the allow list.
  - Default: `[]`
  - Domains in the allow list will bypass the block lists.

- `knot_resolver_rpz_allowlist_name`
  - The name of the allow list RPZ file.
  - Default: `"allowlist"`

- `knot_resolver_rpz_auto_update`
  - A boolean to indicate if automatic update tasks are run.
  - Default: `false`
  - These tasks install a script and set up a service and timer that will update the blocklists on the `knot_resolver_rpz_update_schedule`.

- `knot_resolver_rpz_blocklists`
  - A lsit of dicts of `name`, `url`, and `policy` for blocklists.
  - Default: See the `defaults/main.yml` file.

- `knot_resolver_rpz_update_schedule`
  - The timer unit schedule for updating blocklists.
  - Default: `"*-*-* 06:03:00"`

- `knot_resolver_stub_servers`
  - A list of Knot Authoritative name servers to stub internal domains.
  - Default: `["127.0.1.1@5353"]`

# Example Playbooks

```yaml
---
- name: Configure the knot resolver server. For use on same server as authoritative DNS.
  hosts: kres
  roles:
    - role: cloudcodger.dns.knot_resolver
      knot_resolver_internal_domains:
        - "example.com" # but your real FQDN
        - "1.168.192.in-addr.arpa"
      knot_resolver_rpz_auto_update: true
```

```yaml
---
- name: Configure the knot resolver server. Dedicated resolver.
  hosts: kres
  roles:
    - role: cloudcodger.dns.knot_resolver
      knot_resolver_internal_domains:
        - "example.com" # but your real FQDN
        - "1.168.192.in-addr.arpa"
      knot_resolver_rpz_auto_update: true
      knot_resolver_stub_servers:
        - "192.168.6.86" # The actual IP of you authoritative name server
```
