# ESX Install Scripts
## Building an install repo

Extract your install ISO to a friendly web server, this will be your OS media path in Foreman. If you are using a case sensitive OS for hosting, you will need to rename all the files & folders to lowercase as VMWare have used lowercase in config files;
```sh
find -depth -exec rename 's/(.*)\/([^\/]*)/$1\/\L$2/' {} \;
```

The boot.cfg also requires some minor amends as it is set to look in the root of the server for files;
```sh
sed -i "s/\///g" boot.cfg
```

Finally increase the timeout for loading files;
```sh
echo "timeout=5" >> boot.cfg
```

## iPXE
You will need to make two changes to iPXE, in order for it to support booting the ESXi installer, and then make it;
* src/arch/i386/image/multiboot.c change "MAX_MODULES 8" to "MAX_MODULES 100"
* src/config/local/general.h add "#define IMAGE_COMBOOT"

This can be scripted as;
```sh
git clone git://git.ipxe.org/ipxe.git
sed -i "s/MAX_MODULES 8/MAX_MODULES 100/g" ipxe/src/arch/i386/image/multiboot.c
echo "#define IMAGE_COMBOOT" >> ipxe/src/config/local/general.h
cd ipxe/src
make bin/undionly.kpxe
cp bin/undionly.kpxe /var/lib/tftpboot
```