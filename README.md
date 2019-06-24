# beagle-audio-proxy
USB Soundcard proxy

The ps4 seems to only support USB Class 1 Audio. My audio interface does only support class 2 audio. So I thought let's get something in between to be able to support any usb sound card or audio interface. Any in this case meaning any that is supported on linux. So anything class compliant should work.

The scripts are hard coded to work for my audio interface but should be easy to adjust.

## aplay names
To get the the name that can be used in the script.
    aplay -l

## udev rules
To get the attributes matching your sound card.
    udevadm info --attribute-walk --name=/dev/[your soundcard]

## update the kernel
https://elinux.org/Beagleboard:BeagleBoneBlack_Debian#Debian_Build_Instructions
4.19.x-ti All BeagleBone Variants and BeagleBoard-X15 + RT
    sudo /opt/scripts/tools/update_kernel.sh --ti-rt-channel --lts-4_19

## usb gadget config
kernel documentation for usb audio class 1
https://www.kernel.org/doc/Documentation/ABI/testing/configfs-usb-gadget-uac1

## adjust usb gadget config
in `/opt/scripts/boot/a3...sh`
find function `run_libcomposite` and add usb audio class 1 config
        ...
        # usb audio class 1
        # creates function with sane defaults
        mkdir -p functions/uac1.usb0
        ln -s functions/uac1.usb0 configs/c.1/

        #ls /sys/class/udc
		#v4.4.x-ti
		if [ -d /sys/class/udc/musb-hdrc.0.auto ] ; then
			echo musb-hdrc.0.auto > UDC
		else
			#v4.9.x-ti
			if [ -d /sys/class/udc/musb-hdrc.0 ] ; then
				echo musb-hdrc.0 > UDC
			fi
		fi
        ...

## links
https://elinux.org/Beagleboard:BeagleBoneBlack_Debian#Debian_Build_Instructions
https://www.kernel.org/doc/Documentation/ABI/testing/configfs-usb-gadget-uac1
https://www.collabora.com/news-and-blog/blog/2019/02/18/modern-usb-gadget-on-linux-and-how-to-integrate-it-with-systemd-(part-1)/
