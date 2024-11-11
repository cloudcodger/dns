# Change log

# version 2.0.1

- Fix, now using `lookup('ansible.builtin.vars', zone.records_var)`.

# version 2.0.0

- Changed how primary and secondary zones get configured.
- Updated `README.md`.
- Added `docs` directory with better and more examples of using the `bind9_servers` role.
- Fixed bug for dynamic zone updates not using proper TSIG key.
- Fixed imporperly named include_tasks for dynamic zones.
- Removed `get_md5: false`. Newer version of Ansible no longer supports it.
- Removed `bind9_servers_group` and replaced it with `ansible_play_batch`.
- Changed default value for `bind9_servers_zone_mname`.

# version 1.0.0

- The initial release
