# Ubuntu Postfix Setup

## Software

```
ansible-playbook playbooks/software.yml
```

## Configs

### Run All Configs Setup

```
ansible-playbook playbooks/configs.yml
```

### Available Tasks

```
ansible-playbook playbooks/configs_postfix.yml
ansible-playbook playbooks/configs_opendkim.yml
```

## Additional notes

### Azure additional notes

#### Supposed pre-requisites

* Instance with separate IP-address
* DNS is managed by Cloudflare
* Instance is created on Microsoft Azure cloud

#### Actions

* Create domain record `notifications.domain.ltd IN A x.x.x.x` where x.x.x.x is
  the IP-address of your mailserver
* Add according variables to this role vars and ansible hosts file
* Set up reverse DNS:

  ```
  az network public-ip update \
     --resource-group groupname \
     --name ipname \
     --reverse-fqdn notifications.domain.ltd
  ```

* Generate DKIM keys

  ```
  sudo mkdir -p /etc/opendkim/keys/
  cd /etc/opendkim/keys/
  sudo opendkim-genkey -s mail -d notifications.domain.ltd
  sudo mv mail.private domain.private
  sudo mv mail.txt domain.txt
  sudo chown opendkim:opendkim -R /etc/opendkim
  ```

* Run playbook for this role: `ansible-playbook playbooks/software.yml`
* Add missing DNS records to your DNS zone:

  ```
  sudo cat /etc/opendkim/keys/domain.txt
  ```

  | Record type |              Key              |            Value            |
  |:-----------:|:-----------------------------:|:---------------------------:|
  |      MX     |         notifications         | notifications.domain.ltd 10 |
  |     TXT     | mail._domainkey.notifications |   "v=DKIM1; k=rsa; p=..."   |
  |     TXT     |         notifications         |     "v=spf1 ip4:IP -all"    |

* Test your e-mail by sending something:

  ```
  echo "This is the main body of the mail" | \
       mail -s "Subject of the Email" \
            -a "From: no-reply@notifications.domain.ltd" \
               <yourname>@gmail.com
  ```

