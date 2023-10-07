# telerec-t-vaultwarden
Telerec't submodule for Vaultwarden [1].

## Installation

Add the submodule to your Ansible setup:

```shell
git submodule add https://github.com/ct-Open-Source/telerec-t-vaultwarden.git roles/vaultwarden
```

Then set an admin password. It should be rather long and hard to guess. A long random string is suitable and can easily
be created with:

```shell
openssl rand -base64 48 | tr -d /=
```

Then use this to create an `admin_token`:

```bash
echo -n "admin-password-string" | argon2 "$(openssl rand -base64 32)" -e -id -k 65540 -t 3 -p 4 | sed 's#\$#\$\$#g'
```

Add a section `vaultwarden` to your `group_vars/all.yml` and create a new key named `admin_token` with the output 
of this command. Do not forget to encase it with double quotes.

The admin interface is additionally secured via HTTP-Basic authentication. Create credentials with a hashed password
for the variable `http_basic_users`:

```bash
htpasswd -nb mustermaria Geheimnis| sed -e s/\\$/\\$\\$/g
```

The string in this variable can be a comma separated list of user accounts created all like this.

As a last step create a playbook `vaultwarden.yml` like this in the base folder of your setup:

```yaml
- hosts: server
  become: true
  roles:
    - role: vaultwarden
      vars:
        service_cfg: "{{ vaultwarden }}"
```

## Running the playbook

Start the playbook with: 
```shell
pipenv run ansible-playbook vaultwarden.yml -i hosts
```

You may use tags like this: `pipenv run ansible-playbook vaultwarden.yml -i hosts --tags restarted`

## Reference

[1]   Niklas Dierking, Geheimniskrämer, Der Raspberry Pi als Passwort-Server, c’t 9/2021, S. 18
