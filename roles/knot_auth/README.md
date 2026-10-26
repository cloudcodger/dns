# The knot_auth role

Set up a debian based system as an Authoritative Name Server using `knot`.

This is not a recursive DNS server and is meant to work in conjunction with the `knot_resolver` role.

The role also does not set up a secondary name server. It only creates a primary name server for the zones and would need to be updated in order to support creating primary and secondary name servers. This makes it only useful for small deployments where a single primary name server is adequate for the job.

# Variables

## Required variables.

Although these have default values, the defaults are not usable. See the descriptions for them below.

- `knot_auth_base_domain`
- `knot_auth_zones`

## All Variables.

- `knot_auth_acl`
  - A list of dict of `acl` config items.
  - Default:
    - `address: "127.0.0.0/8"`
  - Keys:
    - `id`: a number (default is the index of the item in the list)
    - `address`: Required and has no default
    - `action`: one of the allowed actions (default is 'transfer')

- `knot_auth_base_domain`
  - The base domain name for other entries.
  - Default: `example.com`
  - Used by: `knot_auth_soa_email_address` and `knot_auth_soa_zone_mname`

- `knot_auth_identity`
  - The config `identity` of the `server`.
  - Default: `"{{ ansible_facts['fqdn'] }}"`

- `knot_auth_listen`
  - A list of `listen` addresses (and ports)
  - Default:
    - `"127.0.1.1@5353"`
    - `"::1@5353"`
  - The default uses port `5353` so that this could be run on the same server as a resolver listening on port `53`.
  - For a system dedicated to just running `knot`, use to listen on all interfaces:
    - `"0.0.0.0@53"`
    - `"::@53"`

- `knot_auth_soa_email_address`
  - The default Email address placed in all SOA records for all zones.
  - Default: `"domainreg.{{ knot_auth_base_domain }}"`

- `knot_auth_soa_zone_mname`
  - the default MName placed in all SOA records for all zones.
  - Default: `"{{ ansible_facts['hostname'] }}.{{ knot_auth_base_domain }}."`

- `knot_auth_storage`
  - The directory containing all zone files.
  - Default: `"/var/lib/knot/zones"`

- `knot_auth_zone_default_expire`
  - The default `expire` value for zone files.
  - Default: `'2419200' # 28 days`

- `knot_auth_zone_default_negative_cache_ttl`
  - the deefault `cache_ttl` value for zone files.
  - Default: `'60' # 1 minute`

- `knot_auth_zone_default_refresh`
  - The default `refresh` value for zone files.
  - Default: `'10800' # 3 hours`

- `knot_auth_zone_default_retry`
  - The default `retry` value for zone files.
  - Default: `'3600' # 1 hour`

- `knot_auth_zone_default_ttl`
  - The default `ttl` value for zone files.
  - Default: `'3600' # 1 hour`

- `knot_auth_zones`
  - A list of dicts for the zones served by the name server.
  - Default: `[]`
  - Keys:
    - `domain`: The FQDN of the zone
    - `file`: The file name for the zone in the `knot_auth_storage` directory (default: `{{ file }}.zone`)
    - `dnssec_signing`: Enable DNSSEC for the zone (default: `true`)
    - `records_var`: The variable name for all the zone records (default: `knot_auth_zone_{{ domain | replace('.', '_') }}`)

# Example Playbooks

Note that the `knot_auth_zone_lab_example_com` and `knot_auth_zone_192_168_1` variables must be set. The recommended place for setting these are in a `host_vars/knot` directory (for the example where `knot` is the hostname), with files names after each variable. This allows them to be as large as needed with each in their own file.

```yaml
---
- name: Configure the knot authoritative name server. For use on same server as resolver.
  hosts: knot
  roles:
    - role: cloudcodger.dns.knot_auth
      knot_auth_base_domain: example.com # but your real FQDN
      knot_auth_zones:
        - domain: "lab.example.com"
          ns:
            - knot.lab.example.com.
        - domain: 1.168.192.in-addr.arpa
          ns:
            - knot.lab.example.com.
          records_var: knot_auth_zone_192_168_1
```

```yaml
---
- name: Configure the knot authoritative name server. Dedicated name server.
  hosts: knot
  roles:
    - role: cloudcodger.dns.knot_auth
      knot_auth_base_domain: example.com # but your real FQDN
      knot_auth_listen:
        - "0.0.0.0@53"
        - "::@53"
      knot_auth_zones:
        - domain: "lab.example.com"
          ns:
            - knot.lab.example.com.
        - domain: 1.168.192.in-addr.arpa
          ns:
            - knot.lab.example.com.
          records_var: knot_auth_zone_192_168_1
```
