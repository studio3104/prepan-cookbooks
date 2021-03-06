# Chef Cookooks for PrePAN

## Setup

### Ruby Libraries

You have to install the gem libraries listed below:

  * knife-solo
  * librarian
  * vagrant
  * ec2ssh

```sh
$ gem install knife-solo librarian vagrant ec2ssh
```

### ec2ssh

Make a file named '~/.ec2ssh' as below:

```
---
aws_keys:
  prepan:
    access_key_id:     your access key
    secret_access_key: your secret access key
regions:
  - us-west-1
```

Then setup your `$HOME/.ssh/config`:

```
$ ec2ssh init --path ~/.ssh/config
$ ec2ssh update --aws_key prepan --path ~/.ssh/config
```

### Vagrant

Add the config to your `~/.ssh/config` to enable SSH login:

```
Host local.prepan.org
  HostName 127.0.0.1
  User vagrant
  Port 2222
  IdentityFile ~/.vagrant.d/insecure_private_key
```

Then add the line below into your `/etc/hosts`:

```
127.0.0.1 local.prepan.org
```

### Chef cookbooks

Download dependent Chef cookbooks with `librarian-chef`:

```sh
$ librarian-chef install
```

## Provision Vagrant

Launch Vagrant at first:

```
$ vagrant up
```

This take a long while at the first time.

Then provision it:

```sh
$ knife solo prepare local.prepan.org
$ knife solo cook local.prepan.org -c config/knife.rb
```

## Provision Hosts on Production (EC2)

Prepare remote host:

```sh
$ knife solo prepare ec2-user@app-1.us-west-1 -i ~/.ssh/prepan.pem -c config/knife.rb
```

Edit `node/${hostname}.json` if it's not thre.

Then provision it:

```sh
$ knife solo cook ec2-user@app-1.us-west-1 -i ~/.ssh/prepan.pem  -c config/knife.rb
```

From the 2nd time, you have to use `deployer` user for login user:

```sh
$ knife solo cook deployer@app-1.us-west-1 -i ~/.ssh/prepan.pem  -c config/knife.rb
```
