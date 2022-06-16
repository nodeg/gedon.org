---
title: "Moving from Thunderbird to NeoMutt"
date: 2020-06-09T18:16:30+02:00
draft: false
---

In order to use Neomutt which is only a mail user agent, you need to install tools to fetch and send mail. I use `mbsync` for delivering mail and `msmtp` for sending mail.

```
$ zypper in mbsync msmtp neomutt
```
# Configuring mbsync

The instructions on how to setup `mbsync` can be found in the man page (`man 1 mbsync`) under the section "configuration". Below you find a basic `.mbsyncrc` configuration file which uses one email account for receiving email.
```
# Account information
IMAPAccount home
Host biela.uberspace.de
Port 993
User max@mustermann.org
PassCmd "pass email/mustermann.org"
SSLType IMAPS

# Remote storage
IMAPStore home-remote
Account home

# Local storage
MaildirStore home-local
Subfolders Verbatim
Path ~/mail/home/
Inbox ~/mail/home/Inbox

Channel home
Master :home-remote:
Slave :home-local:
Patterns * !Archives !Lists

Create Both
Expunge Both
SyncState *
```
## Edit crontab for fetching mail regularly

I added an entry to crontab (`crontab -e`) to check for new mail every 5 minutes.

```
# crontab -l
*/5 * * * * mbsync -a 2>>$HOME/mail/logs/mbsync.log
```

# Configuring msmtp

As with `mbsync` the configuration of `msmtp` can be done by reading its man page (`man 1 msmtp`). Below is a very basic `.msmtp` configuration file.
```
defaults
logfile ~/mail/log/msmtp.log

# Account information
account home
host biela.uberspace.de
port 587
protocol smtp
from max@mustermann.org
auth on
user max@mustermann.org
passwordeval pass email/mustermann.org
tls on
tls_starttls on

account default : home
```

# Configuring NeoMutt

NeoMutt can be configurated very comprehensively according to its [documentation](https://neomutt.org/man/neomuttrc), so I started with adjusting the `sample.neomuttrc-starter` from [here](https://github.com/neomutt/neomutt/blob/master/contrib/sample.neomuttrc-starter). The result can be seen below.
```
# Identity
set realname = "Max Mustermann"
set from = "max@mustermann.org"
set hostname = "mustermann.org"
set folder = "~/mail/home"
set signature = "~/mail/signatures/.signature"
set sendmail = "/usr/bin/msmtp -a home"
set spoolfile = "+Inbox"
set postponed = "+Drafts"
set record = "+Sent"
set mbox = "+Archives/`date +%Y`"
save-hook . "+Archives/%[%Y]"
set trash = "+Trash"
set mbox_type=Maildir

# Colors
source ~/.neomutt/colors.linux

# GPG
unset crypt_use_gpgme
source ~/.neomutt/gpg.rc
set pgp_default_key = "0xxxxxxxxxxxxx"
set crypt_opportunistic_encrypt
set postpone_encrypt
```
