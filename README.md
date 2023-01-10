# user_managent

Powerful Ansible role to manage user accounts on your systems including sshd config. Configures your sshd config, adds and removes users easily, sets their
passwords, groups and authorized_keys.

## Requirements

This role assumes you have `sudo` installed and that the group name for access to `sudo` is "sudo".  
This requirement may drop at a later stage.

## Role Variables

| Variable                                             | Description                                                                                                                                                                                                                                                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `user_management_all_user_attrs`                     | A dictionary of users. See the variables below to see what you can configure per user.                                                                                                                                                                                                                       |
| `user_management_all_user_attrs.<user>`              | Entry for a user. Can have multiple attributes as shown below.                                                                                                                                                                                                                                               |
| `user_management_all_user_attrs.<user>.admin`        | Whether the user is an admin user. If enabled the user will be added to the `sudo` group (and nothing else).<br/><br/>Default: `false`                                                                                                                                                                       |
| `user_management_all_user_attrs.<user>.groups`       | A list of secondary groups the user is in. *Optional*                                                                                                                                                                                                                                                        |
| `user_management_all_user_attrs.<user>.password`     | The **encrypted** password of the user. If not set the user will have `!` set as the encrypted password, which effectively disabled password authentication.<br/><br/>Default: `!`                                                                                                                           |
| `user_management_all_user_attrs.<user>.shell`        | Which default shell the user has.<br/><br/>Default: `/bin/bash`                                                                                                                                                                                                                                              |
| `user_management_all_user_attrs.<user>.ssh_keys`     | An **exclusive** list of authorized SSH keys for that user. *Optional*                                                                                                                                                                                                                                       |
| `user_management_all_user_attrs.<user>.state`        | The state of the user. Used to add or remove a user.<br/><br/>Values:<ul><li>`present` &larr; default<li>`absent`</ul>                                                                                                                                                                                       |
| `user_management_ansible_auth_key`                   | If set this role will add an user called `ansible` that will have that key (or keys if you provide multiple) set as its authorized_keys and will be allowed to sudo without a password. This is useful as a deployment user.<br/>If this is not set or empty, the user will not be added (but not removed!). |
| `user_management_sshd_include`                       | Whether to include the config files under `/etc/ssh/sshd_config.d/*.conf`.<br/>Some sshd versions don't support that setting, so turn it off for those.<br/><br/>Default: `true`                                                                                                                             |
| `user_management_sshd_password_authentication`       | Allow normal users to login with passwords (if set).<br/><br/>Default: `true`                                                                                                                                                                                                                                |
| `user_management_sshd_permit_root_login`             | Allow root user to login via SSH. Can be set to `without-password`.<br/><br/>Values:<ul><li>`true`<li>`without-password` &larr; default<li>`false`</ul>                                                                                                                                                      |
| `user_management_sshd_local_password_authentication` | Same as `user_management_sshd_password_authentication`, but only applies to local connections as defined in `user_management_sshd_local_subnets`                                                                                                                                                             |
| `user_management_sshd_local_permit_root_login`       | Same as `user_management_sshd_permit_root_login`, but only applies to local connections as defined in `user_management_sshd_local_subnets`                                                                                                                                                                   |
| `user_management_sshd_local_subnets`                 | Which subnets to consider "local". If set to an empty list, this feature is disabled.                                                                                                                                                                                                                        |

`user_management_sshd_local_subnets` is generated with this template expression by default (how it works is explained in the `defaults/main.yml`):
```yaml
user_management_sshd_local_subnets: >
  {{
    ansible_facts | dict2items |
    selectattr('key', 'in',
      ansible_interfaces | reject('match', '^(lo|docker[0-9]+)$') | sort
    ) |
    selectattr('value.active', 'true') |
    selectattr('value.ipv4', 'defined') |
    json_query('[].value.ipv4.[network, netmask].join(`/`, @)') |
    map('ansible.utils.ipaddr', 'network/prefix')
  }}
```

If your Ansible is too old (older than 2.11), it'll complain about it not being able to find `ansible.utils.ipaddr`. Change it to `ipaddr` and you're good 
to go!

## Dependencies

Only Ansible Builitins.

## Example Playbook

I personally recommend using `host_vars` or `group_vars`, but a simple copy pastable block looks like this (the keys and passwords aren't real btw):

```yaml
- hosts: servers
  roles:
    - role: brainstone.user_management
      vars:
        all_user_attrs:
          # Existing users
          test:
            state: present
            admin: yes
            groups:
              - testing
            shell: '/bin/zsh'
            password: '$6$e9zHRubiopmvCu4u$3O1FAv04lq8yBfhgfjhgkgheQHaIoFSOK9jTbqtoowcoUfp6liSlbw7c9a001CJu6O.lol4uMnLxrbpk3vOMGVg529oU4dI/'
            ssh_keys:
              - 'ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIH6qb4d6mytVq4W4q9X6DHU24g1UygnmF3do1oC5lkmG cb:6c:cc:c6:ab:64:aa:4b:6b:e9:02:3e:c4:22:6d:c9 Key 1'
              - 'ecdsa-sha2-nistp256 BBBBE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHJghjksgfo78JABBBAlyHkM0Hqglp4VWqnjf7dl0M68YkEr8CK87Ww6s3ZV8DNDbVaLFK++L+qJc+tSxI+5Y3r2sN6Atht9u4= Key 2'
          dummy: { }

          # Removed users
          debian:
            state: absent
```

## License

This Ansible role is licensed under the [MIT License](./LICENSE).

## Contact

To contact me, use GitHub issues or Discord (BrainStone#6759)

## Random Quote

> Every methodology I’ve come across has, at its kernel, a very small section labelled “do magic here”.
>
> — <cite>Katie</cite>
