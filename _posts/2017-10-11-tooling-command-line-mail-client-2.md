---
layout: post
title: "Tooling: command-line mail client"
date: 2017-10-11 11:53:57
categories: linux tips
---
[mutt](http://www.mutt.org/) allows you to access a mailbox via command line and it has a good-enough user interface.

All configuration is kept in ~/.mutt folder. Here’s how I configured it to access different fodlers/accounts navigating via F-keys.

1. Create a account file named like *~/.mutt/account.%accontname%* , for example _account.com.gmail.test_ for _test@gmail.com_. Here’s a template:

    ```
    set my_server = %CHANGEME%
    set my_smtp_server = %CHANGEME%
    set my_user = %CHANGEME%
    set my_pass = %CHANGEME%
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

2. In ~/.mutt/muttrc file register folder-hook and add a macro for each account. In following example test@gmail.com account is registered and mapped to F2.

    ```
    folder-hook 'account.com.gmail.test' 'source ~/.mutt/account.com.gmail.test'
    macro index  'source ~/.mutt/account.com.gmail.test!'
    ```

3. In ~/.mutt/muttrc file define a default account:

    ```
    # Default account
    source ~/.mutt/account.com.gmail.test
    ```

4. Define a shortcut to fetch new emails (in ~/.mutt/muttrc):

    ```
    bind index G imap-fetch-mail
    ```
