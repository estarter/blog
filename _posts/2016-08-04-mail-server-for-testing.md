---
layout: post
title: mail server for testing
date: 2016-08-04 01:12:51
categories: []
tags: []
---

### Set up a mail server

Create a docker-compose.yml:

```
version: '2'

services:
 mail:
 image: tvial/docker-mailserver:latest
 # build: .
 hostname: mail
 domainname: domain.com
 container_name: mail
 ports:
 - "25:25"
 - "143:143"
 - "587:587"
 - "993:993"
 volumes:
 - maildata:/var/mail
 - ./config/:/tmp/docker-mailserver/

volumes:
 maildata:
 driver: local
```

Create your mail accounts

```bash
mkdir -p config
docker run --rm \
 -e MAIL_USER=user1@domain.tld \
 -e MAIL_PASS=mypassword \
 -ti tvial/docker-mailserver:latest \
 /bin/sh -c 'echo "$MAIL_USER|$(doveadm pw -s SHA512-CRYPT -u $MAIL_USER -p $MAIL_PASS)"' &gt;&gt; config/postfix-accounts.cf
```

Start the container:

```docker-compose up -d mail```

[source](https://github.com/tomav/docker-mailserver) or [alternative](https://poste.io/open)

### Send a mail (SMTP)

Install [sendemail](http://caspian.dotconf.net/menu/Software/SendEmail/) to send a mail:

```bash
brew install sendemail
sendemail -f user1@domain.tld -t user1@domain.tld -u test2 -m content -s 0.0.0.0:25 -o tls=no
```

\* [alternative approach](http://tecadmin.net/ways-to-send-email-from-linux-command-line)

### Read a mail (IMAP)

Install mutt ([howto](http://www.lucianofiandesio.com/getting-started-with-mutt-on-osx)) client. Create ~/.muttrc file as following:

```
set my_server = 0.0.0.0
set my_smtp_server = 0.0.0.0
set my_user = user1@domain.tld
set my_pass = mypassword

# Account - SMTP

set smtp_url = "smtp://$my_user:$my_pass@$my_smtp_server:587"
set smtp_pass = $my_pass
set imap_user = $my_user
set imap_pass = $my_pass
set ssl_force_tls = yes
set ssl_starttls = no
set ssl_verify_host = no
set ssl_verify_dates = no


#
# Default inbox
#
set spoolfile=imaps://$my_server/INBOX

#
# Default location of mailboxes
#
set folder=imaps://$my_server/INBOX

bind index G imap-fetch-mail
#bind index d purge-message
```

Start `mutt` to retrieve the mails via smtp, press G to check for updates.
