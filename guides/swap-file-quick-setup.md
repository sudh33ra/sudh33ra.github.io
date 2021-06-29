# Setting up a SWAP file

There might be times when you’ve run out of swap space and it is not practical to repartition a drive or add a new one. In this case, you can use a regular file in an ordinary partition. All you have to do is create a file of the size you want.

## Create File
```bash
dd if=/dev/zero of=/var/my_swap bs=1024 count=131072
chmod 600 /var/my_swap
```
This invocation creates a file called my_swap in /var. It is 128 Mb long (128 x 1024 = 131072). Initially, it is filled with zeros. 

## Activate it
```bash
mkswap -f /var/my_swap
swapon /var/my_swap
```
mkswap marks it as swap space and swapon tells the kernel to start using it as swap space.

## Enable at boot

To enable it at boot, edit /etc/fstab to include the following entry:
```bash
/var/my_swap swap swap defaults 0 0
```

## Deactivate it
```bash
swapoff /var/my_swap
rm /var/my_swap
```

From : http://www.tldp.org/HOWTO/Partition/setting_up_swap.html
