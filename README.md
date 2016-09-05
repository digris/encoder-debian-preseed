mkdir irmod
cd irmod
gzip -d < ../cd/install.amd/initrd.gz | cpio --extract --verbose --make-directories --no-absolute-filenames

# add preseed.cfg

find . | cpio -H newc --create --verbose | gzip -9 > ../cd/install.amd/initrd.gz


cd ../

# rm -fr irmod/



# md5sum

cd cd
md5sum `find -follow -type f` > md5sum.txt
cd ..




# iso (cd only)
genisoimage -o debian-8-odr.iso -r -J -no-emul-boot -boot-load-size 4 -boot-info-table -b isolinux/isolinux.bin -c isolinux/boot.cat ./cd


# iso (usb)
aptitude install xorriso isolinux
xorriso -as mkisofs -o debian-8-odr.iso -isohybrid-mbr /usr/lib/ISOLINUX/isohdpfx.bin -c isolinux/boot.cat -b isolinux/isolinux.bin -no-emul-boot -boot-load-size 4 -boot-info-table ./cd



# bootable usb stick / os x

diskutil list

# hdiutil convert -format UDRW -o debian-8-odr.dmg debian-8-odr.iso

diskutil unmountDisk /dev/disk***
sudo dd if=debian-8-odr.iso of=/dev/disk*** bs=1m
diskutil eject /dev/disk***
