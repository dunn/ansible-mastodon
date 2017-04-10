# mastodansible

Playbook to set up Mastodon on RHEL/CentOS.

## Prerequisites

- Ansible 2.2 or higher

This playbook assumes you already have a server (that you can SSH into
as root) and a domain name pointing at it, and that you want a new
Let’s Encrypt certificate for the domain.

## Running

Edit `inventory` and set `ansible_ssh_host` to your domain.

```
ansible-galaxy install --roles-path roles -r galaxy.yml
ansible-playbook -i inventory toot.yml
```

- You’ll be prompted for the path the public key that you want installed
  on the server at `/home/mastodon/.ssh/authorized_keys`.

- You’ll be prompted for a password for the “mastodon” user.

- You’ll be prompted for SMTP settings; the defaults are for SparkPost.

After reboot, when SELinux is enabled, you’ll need to allow the
application to listen on ports 3000 and 4000:

```shell
sudo semanage port -a -t http_port_t -p tcp 3000
sudo semanage port -a -t http_port_t -p tcp 4000
```

Depending on where the application is running from, you may also need
to configure SELinux to allow NGINX to read thumbnails:

```shell
audit2allow -a –M mastodon # after an image read is blocked
semodule -i mastodon.pp
```
