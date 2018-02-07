# Alfafest Registration App Ansible Playbook

![Ansible Logo](images/AnsibleLogo.png) ![Alfafest Logo](images/AlfafestLogo.png)

This repository holds the Ansible playbook for provisioning the servers used by the yearly Alfafest registration app.
It uses a 1+1 architecture and is composed of Nginx, PHP-FPM, Redis and MariaDB

## Minimum requirements

- Ansible version 2.1
- key based authentification is available and the client can login with ssh without password on all hosts
- 1 gb of swap (optional)
- a public key generated into the server
- python 2 installed in `/usr/bin/python` for dbservers to work properly. A workaround is in place for Ubuntu servers
that have python3 installed (check `hosts`) but Ansible requires python 2 for `mysql-python` module

## Before you start

- make sure you edit the `group_vars/db` and `group_vars/webservers` variables
- you will need to create a `vault.yml` using `ansible-vault` in `group_vars/dbservers/vault.yml` by running:
`ansible-vault create group_vars/webservers/vault.yml`

The file should contain the two passwords needed for mysql:

```
vault_mysql_root_password: <pass>
vault_mysql_ansible_password: <pass>
```

## How to run

```
ansible-playbook -i hosts --ask-vault-pass dbservers.yml
```

## Certificate keys

- As you can see from nginx role and letsencrypt role, the host uses ssl certs for https
- Certs are generated using letsencrypt and they need to be generated on the server.
- Make sure inscrieri.alfaclub.ro is working and app is installed correctly in `/var/www/alfafest/current/public`
- Run `letsencrypt certonly -a webroot --webroot-path=/var/www/alfafest/current/public -d <your-domain>`. This should create the certs
- `openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048` . This should generate a strong Diffie-Hellman group.
- Restart nginx
- If problems, check nginx snippets and `alfafest.conf` file
