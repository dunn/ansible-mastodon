# mastodansible

Playbook to set up Mastodon on RHEL/CentOS.

## Prerequisites

- Ansible 2.2 or higher

- SELinux (i.e., **not** a Linode or Vultr VM)

This playbook assumes you already have a server (that you can SSH into
as root) and a domain name pointing at it, and that you want a new
Let’s Encrypt certificate for the domain.

## Running

Edit `inventory` and set `ansible_ssh_host` to your domain.

```shell
ansible-galaxy install --roles-path roles -r galaxy.yml
ansible-playbook -i inventory toot.yml
```

- You’ll be prompted for the path the public key that you want installed
  on the server at `/home/mastodon/.ssh/authorized_keys`.

- You’ll be prompted for a password for the “mastodon” user.

- You’ll be prompted for SMTP settings; the defaults are for SparkPost.

After it finishes you can deploy using Capistrano.

After reboot, when SELinux is enabled, you’ll need to run the
`post-deploy.yml` playbook to configure SELinux correctly:

```shell
ansible-playbook -i inventory followup.yml -b -K
```

## Troubleshooting

### API Redirects

Like me you might be used to
[redirecting traffic from the naked domain to `www`](http://www.yes-www.org/why-use-www/).
That won’t work here, since several of the API clients involved in
federation do not follow redirects.  If you try to `301` all your
traffic from <https://hello.cat> to <https://www.hello.cat>,
federation will not work.  Nor will trying to log into a Mastodon
client like Amaroq using `hello.cat` as the name of your instance.
