# Config Management: Direct

Use these scripts to get up and runing with:
 * Direct Java Reference Implementation [version 2.1.1](http://wiki.directproject.org/message/view/Java+Reference+Implementation/60702540)

##  Two cloud servers (AWS/EC2) 

Everything runs on your cloud VM, including the ansible installer scripts. (Note that
ansible also supports an ssh-based remote configuraiton mode, where all the
installer scripts run elsewhere and are pushed the the VM when you specify
remote hosts and run `ansible-playbook`.)

We'll focus on local mode, since it's simpler (and only requires a single machine).


##### 1. Provision two fresh Ubuntu 12.04 VM in the cloud (use m1.medium instances)
##### 2.  Install dependencies (ansible) and clone repo of this playbook to your machine
##### 3.  Configuration your environment (see below)
##### 4.  Run this playbook
```
sh ./run.sh
```

### Config files
There are three short files you'll need to edit:

---

##### `settings/direct_server.yml`

* `catchall: true` allows your Direct server to work in "catchall" mode,
where Direct e-mail to `*@yourdomain` will be handled automatically  If catchall mode is disabled,
your Direct server will only be able to receive messages to pre-configured users.

---

##### `hosts`
Update with hosts' IP addresses and path to your SSH keys.

---

##### `host_vars/*`
Update with correct domain names you are using for this setup.

---

##### `group_vars/*`
Update with settings for your particular setup.

---


## Configuring external DNS

In your domain's DNS, add two entries:

* `ns-test-a.amida-demo.com` (record type: `A`, value: `your first EC2 IP`)
* `ns-test-b.amida-demo.com` (record type: `A`, value: `your second EC2 IP`)
* `test-a.amida-demo.com` (record type `NS`, value: `ns-test-a.amida-demo.com`)
* `test-b.amida-demo.com` (record type `NS`, value: `ns-test-b.amida-demo.com`)

With those two entries, you should be up and running!

---
## Testing and Debugging
So you've got the Direct server installed. Have you uploaded your anchor
(`/opt/direct/certificates/certificate.pem`) to the BlueButton+ Trust Bundle?
Upload at: https://secure.bluebuttontrust.org/submitanchor.aspx

Now you can try...

#### Sending a test message to your Direct server
1.  Visit http://sitenv.org/web/sit/direct-transport
2.  Upload your `certificate.der` file
3.  Send a message to yourself (note: use `{anything-you-want}@direct.yourdomain.com` --
anything **except** `catchall@direct.yourdomain.com`!), attaching one of the sample documents (or attach your own -- e.g. from http://github.com/chb/sample_ccdas)


#### Viewing logs
Your server's logs are at:
* `/var/log/upstart/direct-james.log` mail server log
* `/var/log/upstart/direct-dns.log` DNS server log
* `/var/log/upstart/direct-tomcat.log` Config server log

#### Viewing your "catchall" inbox
You can see all the messages that have landed in your inbox using an email client like Thunderbird.
Configure it to talk to your direct server via:

```
Server: your direct_settings.yml (direct_domain_name)
  - POP Port: 995
  - SMTP Port: 465
  - Security: SSL/TLS
  - Authentication: Normal Password
  - username: your direct_settings.yml (email_users.username)
  - password: your direct_settings.yml (email_users.password)
```
