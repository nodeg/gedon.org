---
title: "Moving from KeePassXC to pass"
date: 2020-06-01T19:29:30+02:00
draft: false
---

## Prerequisites

I use several plugins for `pass` and the tool `tomb` to be more flexible and secure:

* [pass-import](https://github.com/roddhjav/pass-import)
* [pass-update](https://github.com/roddhjav/pass-update)
* [pass-audit](https://github.com/roddhjav/pass-audit)
* [pass-tomb](https://github.com/roddhjav/pass-tomb)
* [tomb](https://github.com/dyne/Tomb)

```bash
# requisities for pass-import
zypper in python3-setuptools
pip3 install python3-yaml pykeepass

# pass-import
git clone https://github.com/roddhjav/pass-import/
cd pass-import
make
sudo make install  # For OSX: make install PREFIX=/usr/local

# pass-update
git clone https://github.com/roddhjav/pass-update/
cd pass-update
sudo make install  # For OSX: make install PREFIX=/usr/local

# pass-audit
git clone https://github.com/roddhjav/pass-audit/
cd pass-audit
make
sudo make install

# pass-tomb
git clone https://github.com/roddhjav/pass-tomb/
cd pass-tomb
sudo make install

# Tomb
wget https://files.dyne.org/tomb/Tomb-2.7.tar.gz
tar xvfz Tomb-2.7.tar.gz
cd Tomb-2.7
make install
```

## Generate GPG key to open pass

```bash
gpg --gen-full-key --expert
(...)

pub   ed25519/0xxxxx 2020-06-09 [SC]
      Key fingerprint = xxxx xxxx xxxx xxxx xxxx  xxxx xxxx xxxx xxxx xxxx
uid                              (Password Store) (Password Store) <xxxx@xxxx.de>
sub   cv25519/0xxxxx 2020-06-09 [E]
```

## Initialize pass and tomb

With the newly created GPG key we create a new [tomb](https://github.com/dyne/Tomb) and inside this tomb a initialize the passwort store.

```bash
# pass tomb gpg-id
pass tomb "Password Store (Password Store) <xxxx@xxxx.de>"

 (*) Your password tomb has been created and opened in /home/dom/.password-store.
 (*) Password store initialized for Password Store (Password Store) <xxxx@xxxx.de>
  .  Your tomb is: /home/dom/.password.tomb
  .  Your tomb key is: /home/dom/.password.tomb.key
  .  You can now use pass as usual.
  .  When finished, close the password tomb using pass close.
```

Pass is now initialized and created the tombfile with the key itself and the directory `~/.password-store` where the tomb with the passwords will be mounted (see code above).

After that we create a git repository with pass to track every change.

```bash
pass git init

Initialized empty Git repository in /home/dom/.password-store/.git/
[master (root-commit) 552fb42] Add current contents of password store.
1 file changed, 1 insertion(+)
create mode 100644 .gpg-id
[master ab2a738] Configure git repository for gpg file diff.
1 file changed, 1 insertion(+)
create mode 100644 .gitattributes
```

You can now add a remote repository if desired to distribute or backup the encrypted password files. I will skip this for now.

## Importing passwords from KeePassXC

To import passwords stored in a KeePassXC database I use the tool `pass-import`:

```bash
pass import keepassxc passwords.kdbx

Password for passwords.kdbx:
 (*) Importing passwords from keepassxc to pass
  .  Passwords imported from: dom_private.kdbx
  .  Passwords exported to: /home/dom/.password-store
  .  Number of password imported: 365
  .  Passwords imported:
(...)
```

After the import is finished your all set and can use `pass` according to its documentation (`man 1 pass`).

## Auditing your passwords

To audit your stored passwords, `pass-audit` uses [K-anonymity](https://blog.cloudflare.com/validating-leaked-passwords-with-k-anonymity) to retrieve the knowledge of breached passwords from HIBP server.

```bash
# First open the pass tomb
pass open

# Start the audit process in your home directory
pass audit
```
