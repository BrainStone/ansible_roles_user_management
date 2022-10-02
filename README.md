# user_managent

Powerful Ansible role to manage user accounts on your systems. Adds and removes users easily, sets their passwords, groups and authorized_keys.

## Requirements

This role assumes you have `sudo` installed and that the group name for access to `sudo` is "sudo".  
This requirement may drop at a later stage.

## Role Variables

TODO

## Dependencies

Only Ansible Builitins.

## Example Playbook

I personally recommend using `host_vars` or `group_vars`, but a simple copy pastable block looks like this:

```yaml
- hosts: servers
  roles:
    - role: brainstone.user_management
```

## License

This Ansible role is licensed under the [MIT License](./LICENSE).

## Contact

To contact me, use GitHub issues or Discord (BrainStone#6759)

## Random Quote

> Every methodology I’ve come across has, at its kernel, a very small section labelled “do magic here”.
>
> — <cite>Katie</cite>
