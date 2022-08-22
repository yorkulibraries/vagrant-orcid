Setup a [ORCID](https://github.com/yorkulibraries/orcid) instance quickly with Vagrant and Ansible. The Ansible playbooks can also be used to deploy to a real production server in a similar fashion.


## Getting started

The following steps to provision a *development* instance of ORCID.  

Clone this repo:
```
git clone https://github.com/yorkulibraries/vagrant-orcid.git
```

Change into the vagrant-orcid directory:
```
cd vagrant-orcid
```

Clone the [ansible-rails](https://github.com/yorkulibraries/ansible-rails) project:
```
git clone https://github.com/yorkulibraries/ansible-rails.git
```

Clone the ORCID project for development: (**NOTE:** we use SSH here to clone the ORCID project because we want to be able to make changes.)
```
git clone git@github.com:yorkulibraries/orcid.git
```

Install the Ansible dependencies.

```
ansible-galaxy install -r requirements.yml
```

Bring up the box with RAILS_ENV=development (this is the default):

```
vagrant up
```

Watch for any error/failed tasks. If all is good then the instance is ready to use for testing.

Apache auth_basic is used for Basic Authentication. The default users for the 3 roles: Admin, Manager, and Staff are:

```
admin/demo
manager/demo
staff/demo
```

Edit /etc/hosts and add an entry like followed so you can access the app from a browser at http://orcid.me.ca/

```
192.168.168.168 orcid.me.ca
```

## Verifying emails are sent in development

In development environment, mails are "caught" by MailCatcher. You can see all the emails by going to the MailCatcher web interface at http://papyrus.me.ca:1080/

## Making changes

**NOTE: Assuming you have provisioned the box with the default RAILS_ENV=development.**

The directory **/home/orcid/orcid** in the box is a *symlink* to **/vagrant/orcid**, which is a synced folder in the your local machine's **vagrant-orcid** folder.
You can make changes on your local machine in **vagrant-orcid/orcid** folder and it is changed in the vagrant box too. 

## Running unit tests

**NOTE: Assuming you have provisioned the box with the default RAILS_ENV=development.**

SSH into the box as user **orcid**
```
ssh orcid@127.0.0.1 -p2222
cd orcid
RAILS_ENV=test bundle exec rake db:reset
RAILS_ENV=test bundle exec rake test
```

## Provision a vagrant box with RAILS_ENV=production

If you want to bring the box up with RAILS_ENV=production, then specify the environment variable when you run vagrant up:

```
RAILS_ENV=production vagrant up
```

## Provisioning ORCID on a remote server/VM

Assuming you have a remote server dedicated for running ORCID. And suppose there is a DNS record for the server such as **orcid.yourdomain.ca**.

The following steps will install/configure MYSQL and ORCID on that server.

Create an **inventory** file with the name/IP address of the remote server, similar to the one below:
```
me    ansible_host=192.168.168.168

[rails_app_servers]
me
```

Install Mysql and ORCID on the target server

```
ansible-playbook -i inventory app_provision.yml -e"rails_env=production app_domain=yourdomain.ca mysql_root_password=db_root_password mysql_host=localhost mysql_user=orcid mysql_password=orcid_db_password" --limit me 
```

Install WordPress for /web/ on the target server

```
ansible-playbook -i inventory install_wordpress.yml -e"wp_mysql_password=orcid_wordpress_passwordb" --limit me
```

## About ORCID
Take a look at [ORCID](https://github.com/yorkulibraries/orcid) repo for ORCID code.
