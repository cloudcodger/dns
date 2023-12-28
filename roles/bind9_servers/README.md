Role `bind9_servers`
=========

Install and configure ISC Bind9 on a Primary and zero or more Secondary name servers.

Requirements
------------

The below is required on the host executing `nsupdate` for any dynamic zones configured.

`dnspython`

Because this role allows you to provide invalid data to the configuration, make sure and verify that all zones you configure are working as expected after the playbook has finished. This role assists in configuring Bind 9 but does not validate any of the settings provided.

For example, the Bind 9 software will not load a zone where an NS record points to a name inside the same zone and where a record does not exist for that name. The following zone file for `example.com` will get an error in loading, because there is no A record or CNAME record for `ns1.example.com.`.

```
$TTL 86400
@			IN SOA	ns.example.com. domainreg.example.com. (
				1703627751 ; serial
				10800 ; refresh
				3600 ; retry
				2419200 ; expire
				60 ; minimum
				)

			NS	ns1.example.com.

ctrl1			A	192.168.1.21
ctrl2			A	192.168.1.22
```

Role Variables
--------------

### Required variables.

The default values for the following variables will very likely not be desired or possibly even work.

At a minimum, set the following to desired values.

`bind9_servers_zones`

### All variables.

- `bind9_servers_domain_name` - See the sub-section below for details (including the default value).
- `bind9_servers_email_address` - Email address portion of all SOA records (default: `domainreg.{{ bind9_servers_domain_name }}`).
- `bind9_servers_recursion` - Value for the `recursion` option in `/etc/bind/named.conf.options` (default: `true`).
- `bind9_servers_tsig_algorithm` - Algorithm used for creating TSIG keys (default: `hmac-sha512`). Also used for `nsupdate` calls.
- `bind9_servers_zone_default_expire` - Default expire value all for zone SOA records (default: `2419200` or 28 days).
- `bind9_servers_zone_default_negative_cache_ttl` - Default cache ttl value all for zone SOA records (default: `60`).
- `bind9_servers_zone_default_refresh` - Default refresh value all for zone SOA records (default: `10800`).
- `bind9_servers_zone_default_retry` - Default retry value all for zone SOA records (default: `3600`).
- `bind9_servers_zone_default_ttl` - Default TTL value all for zone SOA records (default: `86400`).
- `bind9_servers_zone_mname` - The MName value for all SOA records (default: `{{ ansible_hostname }}.{{ bind9_servers_domain_name }}.`).
- `bind9_servers_zones` - See the sub-section below for details (default: `[]`).

Special Variable Notes
----------------------

### `bind9_servers_domain_name`

By default this domain name is used as part of `bind9_servers_email_address` and `bind9_servers_mname`.
It is also appended to all NS records, unless they contain a `.` character.
The default for this variable is the first zone name in the `bind9_servers_zones` list (see below).

### `bind9_servers_zones`

This variable must be a list of hashs that contain specific key/value pairs defining the zones to configure.

Required keys:

- `name` - The name of the zone (for example: `example.com` or `1.168.192.in-addr.arpa`).

Optional keys:

- `allowed_tsig_keys` - A list of TSIG key names allowed to perform updates to the zone.
  When set, this designates that the zone is a dynamic zone and causes a TSIG key to be created for the zone.
  This primarily makes sense for a zone of `type primary;` (where neither `forwarders` or `primaries` has been set for the zone) and where it contains the TSIG key name of the zone iteself.
  Otherwise, a TSIG key will be created and included in the `named.conf.local` which could then be used to create a key allowed to update a different zone. Which is not expected to be a common use case.
- `expire` - Override `bind9_servers_zone_default_expire` for the zone.
- `forwarders` - A list of IP addresses to which requests are forwarded.
  When set, this designates the zone as `type forward;` and `forward only;`.
  Takes precedence over `primaries` if both are set.
- `negative_cache_ttl` - Override `bind9_servers_zone_default_negative_cache_ttl` for the zone.
- `ns` - Override `ansible_play_batch` for the NS records for the zone.
  This allows a specific list of host names or FQDNs to be provided for each zone.
  It can be used to not list the primary name server in the NS records or
  to specify one or more load balancers for the NS records.
- `primaries` - A list of IP addresses from which to transfer zone data.
  When set, this designates the zone as `type secondary;`.
  Ignored, if `forwarders` is also set.
- `records_var` - The variable containing the Resource Records (RR) for the zone.
  This will be ignored for zones that are `type forward;` or `type secondary;`.
- `refresh` - Override `bind9_servers_zone_default_refresh` for the zone.
- `retry` - Override `bind9_servers_zone_default_retry` for the zone.

### `records_var` key under `bind9_servers_zones`

Set this to a variable name that will contain a list of the resource records for the zone.

WARNING: Not setting this for a zone will result in no resource records in the zone!

Each Resource Record must be a `;` seperated string of values that represent the following elements.

- Record name (when empty, Bind uses the name from the previous line).
- Record TTL (optional)
- Record Type (for example, `A`, `AAAA`, `PTR`, etc.)
- Record Value (for example, if type is `A` this would be an IP address).

Example Playbook
----------------

See the `docs` directory for different example configurations.
