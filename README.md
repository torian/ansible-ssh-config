# SSH config for Ansible

The idea behind this is that you can have your SSH private keys under git,
but securely stored in an Ansible vault.

The playbook will create an `.ssh` directory on the current path as well as
a `config` file and an `ansible.cfg` file.

## Usage

### Creating an ansible vault

Create a vault file under `vars/vault`, named `ssh_keys.yml`:

```
  $> ansible-vault create vars/vault/ssh_keys.yml
```

And put your keys in there:

```yaml
---
vault_ssh_keys:

  key_1: |
    -----BEGIN RSA PRIVATE KEY-----
    Your private key here
    -----END RSA PRIVATE KEY-----

  key_2: |
    -----BEGIN RSA PRIVATE KEY-----
    Another key
    -----END RSA PRIVATE KEY-----

```

### Configure your ssh/config file

The vars file under `vars/ssh_config.yml` could contain something like:

```
---

ssh_config:

  jumpserver.foo.bar:
    UserKnownHostsFile: /dev/null
    StrictHostKeychecking: no
    User: youruser
    IdentityFile: .ssh/key_1

  somehost.foo.bar:
    UserKnownHostsFile: /dev/null
    StrictHostKeychecking: no
    ProxyCommand: ssh user@jumpserver.foo.bar -i .ssh/key_1 -p 22 nc %h %p
    User: ubuntu
    IdentityFile: .ssh/key_2

```

## Running it

```
  $> ansible-playbook ansible-ssh-config.yml -i localhost, --ask-vault-pass
```

