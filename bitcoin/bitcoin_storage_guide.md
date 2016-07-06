usty's Remarkably Unreliable Guide To Bitcoin Storage: 2016 Edition

## About This Guide

This is an opinionated guide to storing your bitcoin securely.  You
should read this before buying bitcoin.

I wrote this because existing guides are all aimed at experts<sup>[1](#gmax-offline)</sup>,
assume knowledge you might not have, or skip over important things
like backups, or actually spending your bitcoin.

## Check You Have The Right Guide!

This guide is digitally signed by me: you can download it from
[github](https://github.com/rustyrussell/bitcoin-storage-guide/tags)
where you should see a green "Verified" next to the latest version.

For the technically inclined you can also find my GPG fingerprint
here, and my signature on this document in README.md.sig (`gpg --verify README.md.sig README.md`)

* [Keybase](https://keybase.io/rusty)
* [Twitter](https://mobile.twitter.com/rusty_twit/status/644559490646278144)
* [My blog](https://ozlabs.org/~rusty/rusty-gpg.asc)

## About The Author

I'm Rusty Russell, a Linux kernel hacker best known for writing
iptables.  I stumbled into bitcoin a few years ago and promptly got
distracted by all the cool technology; I ended up employed by bitcoin
innovation developers Blockstream because I told them bitcoin is like
Linux and I know Linux (no, really!).

Part of this salary deal was about $5000 of bitcoin per year, paid
monthly after the first year.  Now that first year is up, I decided to
document what I'm doing in the hope that greater minds will provide
feedback on what I did wrong.  And then return my coins please...

# Getting Started

Bitcoins are digital cash; if you get my private key (a big, long,
secret number), you can spend my bitcoins from anywhere in the world.
This makes me nervous, and it should make you nervous too.

Letting someone else hold my coins isn't viable either: the history of
bitcoin is one of scams and hacks, and it will take decades before we
have any idea which bitcoin services are reliable, and which will
collapse and take the funds with them.

There are four main concerns for those holding bitcoin:

1.  **Failure**: All bitcoins will become worthless.
    This is a real possibility, but I can't help you with this one.
2.  **Loss**: I will lose my private key and nobody will be able to spend the coins.
3.  **Theft**: Someone will steal my private key and take my coins.
4.  **Inconvenience**: It will be really hard for me to spend my coins when I want to.

Loss protection, theft protection and convenience are all competing
goals, so I'm going to provide a quick guide:

1.  Beer money.  For less than $50, I'd keep it in any online wallet
    you want.
    * Risks: they could get hacked, go bankrupt, or stop your account.
    * Pros: really easy to use.

2.  Small investment.  For less than $1000, my preference would be the
    Greenbits wallet for Android<sup>[2](#greenbits-android)</sup> (you can also use Green Address's Chrome
    app<sup>[3](#greenaddress)</sup>).  Their initial setup UX is mediocre, but they provide two
    factor for spending above your chosen limit, ability to save your
    wallet phrase in case you lose your phone, a failsafe if they
    ever vanish, and they don't have access to your coins.
    (Disclaimer: I have a potential financial incentive in their success).

    * Risks: if you use SMS verification on the same phone as the
      Greenbits wallet, software which hacks your phone could
      intercept that and spend your money.  If you use a different
      method (eg. phone for SMS verification, laptop Chrome for the
      app), the hacker would have to hack both phone and laptop.
    * Pros: almost as easy to use.

3.  Larger investment.  For less than $10000, you could use a
    hardware wallet with a screen.  The only current option here is
    the Trezor<sup>[4](#trezor)</sup>; it's probably possible to extract the secret key from
    the device, but it'd be fairly hard (thus, not worthwhile) and
    they'd almost certainly need physical access.

    * Risks: if someone hacks your laptop, they could silently change
      the address where you're sending the funds, so you want to
      double-check the address using another device for large
      transfers.  They can't change the amount though.
    * Pros: still fairly easy to use.

4.  Serious investment.  For larger amounts, or the paranoid, a
    completely offline "safe" is a good idea.  That's what this
    document is mainly about, and this is what I'm doing: I plan on
    being at Blockstream for a while, and not spending the bitcoin.

    * Risks: someone hacks your offline machine before you take it offline,
    or someone substitutes the address you're sending the funds to and
    you pay to the wrong place, or you lose the private keys, or you get
    frustrated with how long it takes to spend bitcoin and make a mistake.
    * Pros: most secure against theft.

5.  More money than I will ever have.  For those carrying millions, I
    don't have any useful advice; you need to find a professional who
    won't steal all your money.

# Setting Up An Offline Safe

We're going to use bitcoin-core, the bitcoin reference software, to generate a few bitcoin addresses on a machine which has
no way of contacting the outside world, we're going to write down the
private keys on paper, we're going to double-check them, and we're
going to protect those pieces of paper.

## Create an Offline Safe

You will need:

* A pen
* Four pieces of paper
* Two USB keys (one least 2GB aka "big", one at least 4M aka "small").
* A laptop with two USB ports

### For The Extra Paranoid

The extra paranoid will destroy or never reuse those the USB keys in
any other machine, maim the laptop, ensure it stays offline forever or
destroy it: you can simply buy a cheap $200 laptop or use an old one.

### Step 1: Download and Prepare Ubuntu 16.04 (20 minutes)

Ubuntu is a simple, free operating system.  It's easy to install and
use, and downloaded thousands of times each day.  You can get it from
[http://releases.ubuntu.com/16.04/ubuntu-16.04-desktop-amd64.iso](http://releases.ubuntu.com/16.04/ubuntu-16.04-desktop-amd64.iso).
My test laptop was so over 10 years old so it didn't support 64 bit (I
got a message about "This kernel requires an x86-64 CPU"), so I used
[http://releases.ubuntu.com/16.04/ubuntu-16.04-desktop-i386.iso](http://releases.ubuntu.com/16.04/ubuntu-16.04-desktop-i386.iso)
instead.

You should check that you have the real Ubuntu, if you can.  On Linux
and MacOS this is easy, on Windows you'll need
[sha256sum.exe](http://www.labtestproject.com/files/win/sha256sum/sha256sum.exe).
Use the following commands to sum the file you downloaded, which should
match the example below:

* **MacOS**: shasum -a 256 /tmp/ubuntu-16.04-desktop-amd64.iso
* **Windows**: sha256sum.exe ubuntu-16.04-desktop-amd64.iso
* **Linux**: sha256sum /tmp/ubuntu-16.04-desktop-amd64.iso

This should give a number
<!--- sha256sum ubuntu-16.04-desktop-amd64.iso --->
`4bcec83ef856c50c6866f3b0f3942e011104b5ecc6d955d1e7061faff86070d4`

(For 32-bit Ubuntu, the number is
<!--- sha256sum ubuntu-16.04-desktop-i386.iso --->
`b20b956b5f65dff3650b3ef4e758a78a2a87152101a04ea1804f993d8e551ceb`
instead).

If the number you get is different, STOP.  Something is badly wrong.

Now we need put it on the big USB, so we can install it on the cheap
laptop; here are instructions for
[Windows](http://www.ubuntu.com/download/desktop/create-a-usb-stick-on-windows),
[MacOS](http://www.ubuntu.com/download/desktop/create-a-usb-stick-on-mac-osx)
and
[Linux](http://www.ubuntu.com/download/desktop/create-a-usb-stick-on-ubuntu).

#### Extra Paranoia (Optional)

Use DVDs instead of the USB sticks, and a laptop which doesn't hcc6d955d1e7061faff86070d4`

(For 32-bit Ubuntu, the number is
<!--- sha256sum ubuntu-16.04-desktop-i386.iso --->
`b20b956b5f65dff3650b3ef4e758a78a2a87152101a04ea1804f993d8e551ceb`
instead).

If the number you get is different, STOP.  Something is badly wrong.

Now we need put it on the big USB, so we can install it on in--- sha256sum ubuntu-16.04-desktop-i386.iso --->
`b20b956b5f65e e cheap
laptop; here are instructions for
[Windows](http://wwwn/+archive/ubuntu/bitcoin/+files/bitcoind_0.12.1-xenial1_amd64.deb)
*  [db4.8++](https://launchpad.net/~bitcoin/+archive/ubuntu/bitcoin/+files/libdb4.8++_4.8.30-xenial2_amd64.deb)
*  [libboost-chr we need put it on the big USB, so we can install it on in--- sha256sum ubuntu-16.04-desktop-i386.iso --->
`b20b956b5f65e e cheap
laptop; here are instructions for
[Windows](http://wwwn/+archive/ubuntu/bitcoin/+files/bitcoind_0.12.1-xenial1_amd64.deb)
*  [db4.8++](https://launchpad.net/~bitcoin/+archive/ubuntu/bitcoin/+files/libdb4.8++_4.8.30-xenial2_amd64.deb)
*  [libboost-chr we need put it on the big USB, so we can install it on in--- sha256sum ubuntu-16.04-desktop-i386.iso --->
`b20b956b5f65e e cheap
laptop; here are instructions for
[Windows](http://wwwn/+archive/ubuntu/bitcoin/+files/bitcoind_0.12.1-xenial1_amd64.deb)
*  [db4.8+2_a256sum ubuntu-16.04-desktop-i386.iso --->
`b20b956b5f65e e chmaor
[Windows](http://wwwn/+archive/ubuntu/bitcoin/+files/bitcoind_0.12.1-xenial1_amd64.deb)
*  [ub_0.12.1-xenial1_amd64.dencode/qrencode_3.4.4-1_amd64.deb)
*  [libqrencode](https://mirrors.kernel.org/ubuntu/pool/universe/q/qrencode/libqrencode3_3.4.4-1_amd64.deb)

Here are the 32-bit versions if your system is too old:

*  [bitcoind](https://launchpad.net/~bitcoin/+archive/ubuntu/bitcoin/+files/bitcoind_0.12.1-xenial1_i386.deb)
*  [db4.8++](https://launchpad.net/~bitcoin/+archive(https://launchpad.net/~bitcoin/+archive/ubuntu/bitcoin/+files/libdb4.8++_4.8.30-xenial2_amd64..ktall it on in--- sha256sum ubuntu-16.04-desktop-i386.iso --->
`b20b956b5f65e e cheap
laptop; here are instructions for
[Windows](http://wwwn/+archive/ubuntu/bi/pu/bitcoin/+files/bitcoind_0.12.1-xenial1_amd64.deb)
*  [ub_0.12.1-xenial1_amd64.dencode/qrencode_3.4.4-1_amd64.deb)
*  [libqrencode](https://mirrors.kernel.org/ubuntu/pool/universe/q/qrencode/libqrencode3_3.4.4-1_amd64.deb)

Here are the 32-bit versions if your system is too old:

*  [bitcoind](https://launchpad.net/~bbl1-xenial1_amd64.dencode/qrencode_3.4.4-1_amd64.deb)
*  [libqreg/buntu/pool/univerlibe/libevent/libevent-pthreads-2.0-5_2.0.21-stable-2_i386.deb)
*  [libssl](http://security.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.0.0_1.0.2g-1ubuntu4.1_i386.deb)
*  [qrencode](https://mirrors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.net/~bitcoin/+archive(httket on in--- sha256sum ubuntu-16.04-desktop-i386.iso --->
`b20b956b5f65e e cheap
laptop; here are instructions for
[Windows](http://wwwn/+archive/ubuntu/bi/pu/bi ostem is too old:

*  [bitcoind](https://launchpad.net/~bbl1-xenial1_amd64.dencode/qrencode_3.4.4-1_amd64.deb)
*  [libqreg/buntu/pool/univerlibe/libevent/libevent-pthreads-2.0-5_2.0.21-stable-2_i386.deb)
*  [libssl](http://security.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.0.0_1.0.2g-1ubuntu4.1_i386.deb)
*  [qrencode](https://mirrors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad. surity.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.0.0_1.0.2g-1ubuntu4.1_i386.deb)
*  [qrencode](https://mirrors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.net/~bitcoin/+archive(httket on" (https://mirrors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.p /launchpad.net/~bbl1-xenial1_amd64.dencode/qrencode_3.4.4-1_amd64.deb)
*  [libqreg/buntu/pool/univerlibe/libevent/libevent-pthreads-2.0-5_2.0.21-stable-2_i386.deb)
*  [libssl](http://security.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.0.0_1.0.2g-1ubuntu4.1_i386.deb)
*  [qrencode](https://mirrors.kernel.org/ubuntu/poF1ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.0.0_1.0.2g-1ubuntu4.1_i386.deb)
*  [qrencode](https://mirrors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.net/~bitcoin/+archive(httket on" (https://mirrors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.p /launchpad.net/~bbl1-xenial1_amd64.dencode/qrencode_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.net/~b o4.8++](https://launchpad.net/~bitcoin/+archive(httket on" (htt y/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.p /launchpad.net/~bbl1-xenial1_amd64.de  rors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.net/~bitcoin/+archive(httket on" (https://mirrors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.p /launchpad.net/~bbl1-xenial1_amd64.dencode/qrencode_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.net/~b o4.8++](https://launchpad.net/~bitcoin/+archive(httket on" (htt y/universe/q/qrencod.deb)
*  [db4.8++]lerse/q/qrencod.deb)
*  [db4.8++](https://launchpad.p /launchpad.net/~bbl1-xenial1_amd64.dencode/qrencode_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/pool/uning(https://launchpad.net/~bitcoin/+archive(httket on" (htt y/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.p /launchpad.net/~bbl1-xenial1_amd64.de  rors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.net/~b o4.8++](https://launchpad.net/~bitcoin/+archive(httket on" (htt y/universe/q/qrencod.deb)
*  [db4.8++]lerse/q/qrencod.deb)
*  [db4.8++](https://launchpad.p /launchpad.net/~bbl1-xenial1_amd64.dencode/qrencode_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/pool/uning(https://launchpad.net/~bitcoin/+archca_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/pool/uning(https://launchpad.net/~bitcoin/+archive(httket on" (htt y/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.p /launchpad.net/~bbl1-xenial1_amd64.de  rors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.net/~b o4.8++](https://launchpad.net/~bitcoin/+archive(httihpad.p /launchpad.net/~bbl1-xenial1_amd64.de  rors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](https://launchpad.net/~b o4.8++](https://launchpad.net/~bitcoin/+archive(httihpad.p /launchpad.net/~bbl1-xenial1_amd64.de  rors.kernel.org/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrenco 5rs.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrenco 5rs.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrenco 5rs.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.dembg/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrenco 5rs.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.dembg/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kechtu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.dembg/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kenshttho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrenco 5rs.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.dembg/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++inel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kechtu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.dembg/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.ke91erse/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kenshtths 5rs.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.dembg/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++inel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kechtu/proors.kernel.peorg/ubuntu/pool/universe o3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.ke91erse/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.deb)
*  [db4.8++](httho_3.4.4-1_amd64.dewo://mirrors.kernel.org/ubuntu/proors.kenshtths 5rs.kernel.org/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencod.dembg/ubuntu/proors.kernel.peorg/ubuntu/pool/universe/q/qrencers so they'll mine your
   transaction.  I have no idea what this will be: 30000 satoshi per
   kilobyte is currently reasonable.  Google for "bitcoin fee estimator"
   and you'll find several.

5. (Optional) Another address in your offline safe to send the
   change to, if you're not sending the entire amount.

6. A phone camera or QR code scanner to get the resulting transaction out.

Now you have those:

1. Turn your laptop on, booting off the big USB key again.  Turn
   off networking, select "Try Ubuntu", put in the small USB key, open
   a terminal, press F11, and run `python3 /media/ubuntu/*/offline`.

2. Restore the private key which received the payment.

3. Now enter the transaction you received (slow way) or the
   transaction ID (quick way).

4. Slow way: the program will now look through the transaction for
   which output paid to you, and how much it was.  If you typed the
   transaction wrong, it probably will fail to work when you try to broadcast
   the final transaction (it may simply fail to decode the transaction
   immediately, if you're lucky).

5. Quick way: you will tell it the output number and the amount that
   was sent.  If you get the output number or transaction ID wrong, it
   will fail when you try to broadcast the final transaction.  If you
   get the amount wrong (too high) it will also fail then.  If you get
   the amount wrong (too low) it will pay the difference to miners and
   you will lose it.  Be very careful that the amount is correct!

6. Now enter the fee rate, and address to pay to.  Fees are required to
   get miners to include your transaction in a block.

7. Now enter the amount to send: to send it all (minus the fee), just
   hit enter.

8. If you didn't elect to send it all, enter one of your public
   addresses to receive the change.  I don't recommend reusing
   addresses, since that makes it obvious which output is payment and
   which is change.

9. It will complain if the change amount is tiny, or the fee amount
seems huge.

10. It will then describe the transaction; check this is correct.  If
    you elected to receive change, write down the transaction id,
    output and amount for spending in future.

11. Press Enter and it will then show you the completed, raw transaction
    as a QR code.  Google for "bitcoin send raw transaction" and
    you'll see various web sites where you can paste it in to send it
    to the bitcoin network.

12. If you don't want the transaction, don't send it: you can simply
    try again to create a new one.

### Extra Paranoia (Optional)

Run your own bitcoin full node somewhere (online).  Use `bitcoin-cli
importaddress 1DzQg9vuzBGUnAB5Z6vgZX8qabbM1ftxDf` (except with your
own public key).  You can do this once for each address, and use
`bitcoin-cli getbalance` to see your bitcoins.  Use `bitcoin-cli
listtransactions "*" 1000 0 true` to show received and sent
transactions.  Use `bitcoin-cli getrawtransaction` to get a particular
raw transaction.  Use `bitcoin-cli estimatefee 6` to get the fee rate.

If you need to use a block explorer to find transactions, connect via
Tor.

# Final Words On Security

No security is perfect, but it's even more common for people to lose
their keys altogether.  Make sure your loved ones know your bitcoins
exist, and where to ask for help if you get struck by lightning.

Good luck!

Rusty.

# References and Other Resources

<a name="gmax-offline">[1]</a> [Greg Maxwell's offline signing example with bitcoin 0.7](https://people.xiph.org/~greg/signdemo.txt)

<a name="greenbits-android">[2]</a> [Greenbits Wallet for Android](https://play.google.com/store/apps/details?id=com.greenaddress.greenbits_android_wallet&hl=en) (NOT the older GreenAddress wallet, which has usability issues)

<a name="greenaddress">[3]</a> [Green Address](https://greenaddress.it/)

<a name="trezor">[4]</a> [The Trezor Hardware Wallet](https://bitcointrezor.com/)
