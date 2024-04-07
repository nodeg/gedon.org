+++
title = "Tumbleweed with LUKS2 and Argon2"
date = 2024-03-31T16:01:47+02:00
draft = false
+++

The steps below show you how to install openSUSE Tumbleweed with [LUKS2](https://gitlab.com/cryptsetup/LUKS2-docs/blob/main/luks2_doc_wip.pdf) and [Argon2id](https://datatracker.ietf.org/doc/html/rfc9106) as Password-Based Key Derivation Function (PBKDF) when using [systemd-boot](https://news.opensuse.org/2024/03/05/systemd-boot-integration-in-os)[^1] instead of GRUB2 as bootloader.

1. Perform an installation with full disk encryption
1. Select `Guided Setup` during partitioning the disk and use LVM
    ![Image LVM](/post/tw_luks2/lvm.png)
1. Change the bootloader by selecting 'Booting' at the top ot the summary screen
    ![Image summary page](/post/tw_luks2/confirmation.png)
1. Select `Systemd Boot` as bootloader
    ![Image systemd-boot](/post/tw_luks2/systemd-boot.png)
1. After the installation is finished, boot into the rescue system of the installation medium.
  The LUKS conversion cannot be done while the device is in use.
    ![Image rescue system 1](/post/tw_luks2/rescue_1.png)
    ![Image rescue system 2](/post/tw_luks2/rescue_2.png)
1. Login as `root`
1. Convert `LUKS1` to `LUKS2` with:

    ```bash
    cryptsetup convert --type luks2 /dev/sdaX
    ```

1. Check the result with:

    ```bash
    $ cryptsetup luksDump /dev/sdaX

    LUKS header information
    Version:        2
    Epoch:          4
    Metadata area:  16384 [bytes]
    Keyslots area:  2064384 [bytes]
    UUID:           XXXXXXXXXXXXXXXXXXXXX
    Label:          (no label)
    Subsystem:      (no subsystem)
    Flags:          (no flags)

    Data segments:
    0: crypt
            offset: 2097152 [bytes]
            length: (whole device)
            cipher: aes-xts-plain64
            sector: 512 [bytes]

    Keyslots:
    0: luks2
            Key:        512 bits
            Priority:   normal
            Cipher:     aes-xts-plain64
            Cipher key: 512 bits
            PBKDF:      PBKDF2
            Time cost:  7
            Memory:     1048576
            Threads:    4
            Salt:       XXXXXXXXXXXXXXXXXX
            AF stripes: 4000
            AF hash:    sha256
            Area offset:290816 [bytes]
            Area length:258048 [bytes]
            Digest ID:  0
    Tokens:
    Digests:
    0: pbkdf2
            Hash:       sha256
            Iterations: 129774
            Salt:       XXXXXXXXXXXXXXXXXX
            Digest:     XXXXXXXXXXXXXXXXXX
    ```

1. Change the PBKDF:

    ```bash
    cryptsetup luksConvertKey --pbkdf argon2id /dev/sdaX
    ```

1. Check the result again with:

    ```bash
    $ cryptsetup luksDump /dev/sdaX

    LUKS header information
    Version:        2
    Epoch:          4
    Metadata area:  16384 [bytes]
    Keyslots area:  2064384 [bytes]
    UUID:           XXXXXXXXXXXXXXXXXXXXX
    Label:          (no label)
    Subsystem:      (no subsystem)
    Flags:          (no flags)

    Data segments:
    0: crypt
            offset: 2097152 [bytes]
            length: (whole device)
            cipher: aes-xts-plain64
            sector: 512 [bytes]

    Keyslots:
    0: luks2
            Key:        512 bits
            Priority:   normal
            Cipher:     aes-xts-plain64
            Cipher key: 512 bits
            PBKDF:      argon2id
            Time cost:  7
            Memory:     1048576
            Threads:    4
            Salt:       XXXXXXXXXXXXXXXXXX
            AF stripes: 4000
            AF hash:    sha256
            Area offset:290816 [bytes]
            Area length:258048 [bytes]
            Digest ID:  0
    Tokens:
    Digests:
    0: pbkdf2
            Hash:       sha256
            Iterations: 129774
            Salt:       XXXXXXXXXXXXXXXXXX
            Digest:     XXXXXXXXXXXXXXXXXX
    ```

1. Reboot into your system

Regarding the used cipher, you can benchmark the available ones with

```bash
$ cryptsetup benchmark

# Tests are approximate using memory only (no storage IO).
PBKDF2-sha1      1528536 iterations per second for 256-bit key
PBKDF2-sha256    2092966 iterations per second for 256-bit key
PBKDF2-sha512    1546572 iterations per second for 256-bit key
PBKDF2-ripemd160  897753 iterations per second for 256-bit key
PBKDF2-whirlpool  691672 iterations per second for 256-bit key
argon2i       7 iterations, 1048576 memory, 4 parallel threads (CPUs) for 256-bit key (requested 2000 ms time)
argon2id      7 iterations, 1048576 memory, 4 parallel threads (CPUs) for 256-bit key (requested 2000 ms time)
#     Algorithm |       Key |      Encryption |      Decryption
        aes-cbc        128b      1282.2 MiB/s      3857.8 MiB/s
    serpent-cbc        128b       104.2 MiB/s       820.8 MiB/s
    twofish-cbc        128b       242.6 MiB/s       435.4 MiB/s
        aes-cbc        256b       962.9 MiB/s      3095.0 MiB/s
    serpent-cbc        256b       107.9 MiB/s       819.7 MiB/s
    twofish-cbc        256b       248.5 MiB/s       426.4 MiB/s
        aes-xts        256b      3825.8 MiB/s      3821.4 MiB/s
    serpent-xts        256b       715.6 MiB/s       723.8 MiB/s
    twofish-xts        256b       388.7 MiB/s       390.9 MiB/s
        aes-xts        512b      3086.5 MiB/s      3083.5 MiB/s
    serpent-xts        512b       710.5 MiB/s       710.8 MiB/s
    twofish-xts        512b       384.4 MiB/s       385.9 MiB/s
```

and decide if you want a different cipher instead of `aes-xts-plain64`. The results above represent an Intel i7-8665U.

[^1]: When using `systemd-boot` instead of `GRUB2`, the decryption time for the LUKS2 volume decreased from 29 s to ~2.5 s for me.
