# preseed workflow

## preparation

### download & mount iso

wget http://cdimage.debian.org/debian-cd/8.6.0/amd64/iso-cd/debian-8.6.0-amd64-netinst.iso

mkdir loopdir
mount -o loop debian-8.6.0-amd64-netinst.iso loopdir
mkdir cd
rsync -a -H --exclude=TRANS.TBL loopdir/ cd
umount loopdir




### extract cd installer

mkdir irmod
cd irmod
gzip -d < ../cd/install.amd/initrd.gz | cpio --extract --verbose --make-directories --no-absolute-filenames

### add preseed.cfg

cd irmod
find . | cpio -H newc --create --verbose | gzip -9 > ../cd/install.amd/initrd.gz


cd ../

### rm -fr irmod/



### installer boot menu - autostart

nano cd/isolinux/isolinux.cfg
timeout 10







### md5sum

cd cd
md5sum `find -follow -type f` > md5sum.txt
cd ..



### create iso file

#### iso (cd only)
genisoimage -o debian-8-odr.iso -r -J -no-emul-boot -boot-load-size 4 -boot-info-table -b isolinux/isolinux.bin -c isolinux/boot.cat ./cd


#### iso (usb)
aptitude install xorriso isolinux
xorriso -as mkisofs -o debian-8-odr.iso -isohybrid-mbr /usr/lib/ISOLINUX/isohdpfx.bin -c isolinux/boot.cat -b isolinux/isolinux.bin -no-emul-boot -boot-load-size 4 -boot-info-table ./cd



### bootable usb stick / os x

diskutil list


diskutil unmountDisk /dev/disk***
sudo dd if=debian-8-odr.iso of=/dev/disk*** bs=1m
diskutil eject /dev/disk***


### bootable usb stick / debian

fdisk -l


sudo dd if=debian-8-odr.iso of=/dev/sd*** bs=1M

