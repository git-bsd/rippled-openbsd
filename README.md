# rippled-port-for-openbsd

My attempt at porting rippled to openbsd 
https://openbsd.org
https://xrpl.org
https://github.com/ripple/rippled

#### does this work? 
NO.
Something is wrong with the commandline interface, logs seem to show it does do some stuff.
Coredumps pretty quickly..
Will investigate further.

## Motivations
1. I want to learn more about UNIX like operating systems.
2. Learn more about the rippled software.
3. Potential to distribute the base operating system layer to possibly make the distributed network more resilient.

## Should I use this?
Probably not, I have found that on a testnet it runs alright.

My experience is not broad enough to **know** that this will run a production grade system.

## How do I install this?
*warning* 
I do NOT recommend you install this on a system you use for things other then testing.
This will probably break stuff, so please take care to just get a fresh vm or chroot to install.

I did do my best thus far to have the install and uninstall process clean up after itsself.

Before you start, you need a working ports tree see the working with ports section https://www.openbsd.org/faq/ports/
``` sh
git clone https://github.com/git-bsd/rippled-openbsd
cp -r rippled-port-for-openbsd/mystuff /usr/ports/
cd /usr/ports/mystuff/net/rippled/
doas make install-depends
```
**make some coffee**

Because this port is not in the official ports tree yet, manually add the _rippled user to user.list
This could cause errors if in between me posting this, and you building this a new user has been added
It is best to not blindly execute the below command, but check first if there is a user with the uid/gid 
```sh
echo "877 _rippled        _rippled    mystuff/misc/rippled" >> /usr/ports/infrastructure/db/user.list
```
Next, we are ready to make the package with
```sh
doas make install
```

## Running it
Make sure to read the comments in the cfg file, and configure it properly.
To just start up the daemon;
```
doas rcctl start rippled
```
If you want it to start at startup;
```
doas rcctl enable rippled
```
As of now, rippled.cfg and validators.txt are placed in /etc/rippled/

```
doas rippled --conf=/etc/rippled/rippled.cfg server_info
```
Please beware, I have tested this on the testnet but have not had success on the mainnet.
I do reconmend you to only use a testnet https://xrpl.org/connect-your-rippled-to-the-xrp-test-net.html

BEWARE,
I have decided to put the rippled data in /var/run/rippled
In a base instll, this partition is not that big.
To make sure you dont run out of diskspace on this partition, it might be a good idea to mount a new partition at /var/run/rippled!

Other workaround could be to let rippled store data in /home/whatever
Make sure the directory is owned and writable by _rippled


## TODO
1. test reporting mode. also validation needs to be tested still.
2. Search around for proper logrotation. the cronjob thingy is messy
3. Do more checking of added and delete files
4. Fix the manpages
