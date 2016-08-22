# Kernel

We'll start with the kernel, it's the core of the phone and sooner or later, you'll need to modify it. Right now, just Huawei/Honor distributes Kirin SoCs, so you'll find it at their webpage. On some devices, for whatever reason, the source is just released at the chinese version of their webpage, so try with that if you aren't lucky.

Now that you have the kernel, it's time to test it, Huawei/Honor builds their kernels with the Arm64 toolchain found at Google's git server, it can also be found inside the 'prebuilts/gcc' folder if you downloaded the AOSP/CM source, some kernels have instructions inside because sometimes Huawei/Honor release more than one defconfig with a similar name, so reading them will help.

Once compiled, test it to check that everything is working as expected, sometimes Huawei/Honor leave some essential drivers commented out, so it might be helpful to check the defconfig file for any missing driver.

Note that Huawei/Honor (as of August 2016) don't release the kernel source for every EMUI update, so the kernel source that they provide might work just with some android versions, if you are working on a custom rom, make sure to use the files that you know that fully work with the kernel you have.
