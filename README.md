# user_managent

Powerful Ansible role to manage user accounts on your systems including sshd config. Configures your sshd config, adds and removes users easily, sets their
passwords, groups and authorized_keys.

## Requirements

This role assumes you have `sudo` installed and that the group name for access to `sudo` is "sudo".  
This requirement may drop at a later stage.

## Role Variables

| Variable                                         | Description                                                                                                                                                                                                                                                                                                  |
|--------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `user_management_ansible_auth_key`               | If set this role will add an user called `ansible` that will have that key (or keys if you provide multiple) set as its authorized_keys and will be allowed to sudo without a passowrd. This is useful as a deployment user.<br/>If this is not set or empty, the user will not be added (but not removed!). |
| `user_management_all_user_attrs`                 | A dictionary of users. See the variables below to see what you can configure per user.                                                                                                                                                                                                                       |
| `user_management_all_user_attrs.<user>`          | Entry for a user. Can have multiple attributes as shown below.                                                                                                                                                                                                                                               |
| `user_management_all_user_attrs.<user>.admin`    | Whether the user is an admin user. If enabled the user will be added to the `sudo` group (and nothing else).<br/><br/>Default: `false`                                                                                                                                                                       |
| `user_management_all_user_attrs.<user>.groups`   | A list of secordary groups the user is in. *Optional*                                                                                                                                                                                                                                                        |
| `user_management_all_user_attrs.<user>.password` | The **encrypted** password of the user. If not set the user will have `!` set as the encrypted password, which effectively disabled password authentification.<br/><br/>Default: `!`                                                                                                                         |
| `user_management_all_user_attrs.<user>.shell`    | Which default shell the user has.<br/><br/>Default: `/bin/bash`                                                                                                                                                                                                                                              |
| `user_management_all_user_attrs.<user>.ssh_keys` | An **exclusive** list of authorized SSH keys for that user. *Optional*                                                                                                                                                                                                                                       |
| `user_management_all_user_attrs.<user>.state`    | The state of the user. Used to add or remove a user.<br/><br/>Values:<ul><li>`present` &larr; default<li>`absent`</ul>                                                                                                                                                                                       |

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
