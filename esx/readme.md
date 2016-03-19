# ESX Install Scripts
## iPXE changes

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

## Prepping the install media
Drop everything on your ESXi installation media to a handy http server. In the boot.cfg remove every "/" from the file so the installer will look in the same path as the boot.cfg file.
