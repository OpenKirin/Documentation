# Modem

As you might have realized, the HiSilicon Kirin SoCs include their own modem, that means, that you'll have to do some extra patching to get them up and running.

There are 4 parts involved:

* The kernel
* The userspace libraries
* Phone.prop
* The framework

## Kernel

First of all, the modem is one of those things that tends to be left out of the kernel source releases, so make sure that it's not missing, it's usually located at ´drivers/hisi/modem_hixxxx´.

Once that We now that the modem source is present, and that it works (test it while you are on EMUI).

## Userspace libraries

We are getting closer! Based on my experience, you'll obviously need to include to the vendor blobs list the RIL (Radio Interface Layer) daemon, located at ´bin/rild´. 

Once you have done that, you'll also have to include the RIL libraries that will communicate with the modem, a simple ´readelf -h bin/rild´ reveals that rild is an ELF64 executable, so We should be looking at the lib64 directory.

The relevant libraries here are `libbalong-ril.so, libbalong-ril-1.so, libril.so, librilutils.so and libhuawei-audio.ril.so`. But since it may be different for your device I'll try to help you to figure out what libraries do you need.

Running ´readelf -d bin/rild´ shows that it depends on libril.so, and libril.so depends on librilutils.so. About the libbalong libs, I'll show you how I figured it out in the next phase.

## Phone.prop

Inside this file you'll find some RIL relevant settings. By just opening it, We can see some lines like ´rild.libpath´ and ´rild.libargs´ but these settigns are repeated all over the file! That's beacuse Huawei/Honor wanted to have one generic file. The section where you see lib64 will probably be the one We're after, so just copy it to build.prop.

## Framework

Here is where I got stuck for months, since rolling your own workaround requires Java coding. However, I found a useful java class coded by codeworkx that did the trick, I also had to patch another files, here you'll find the original source "https://github.com/mt7-dev/aosp_frameworks_opt_telephony/commits?author=gabry3795" just make sure to patch the framework like He did.

## Conclusion

As I have explained above, the modem is a complex system, and it will take time and a lot of testing if you want it to work properly, you might have to patch the signal values at HuaweiRIL.java but once it works you'll be a step further from completing your rom!


PS: As always, I have written this text from what I remember when I had to fix it in my own rom, so you'll probably have to fix some missing depencencies or find your own hack.
