# Graphics

The first system that you'll want to have working in your custom rom is the Graphics one. Most (if not all) Kirin SoCs have an ARM Mali GPU, ARM provides open source kernel drivers (Use the fbdev one), but the userspace driver is proprietary and you'll have to extract it from your stock rom. Userspace and Kernel drivers are paired, so you can't update the kernel driver without updating the userspace driver. The userspace driver can be found either inside '/system/lib64/egl/' or '/vendor/lib64/egl' and it's called most of the times libGLES_mali.so However, this is not the only library you'll have to copy to see that sweet boot animation.

Huawei worked on their own memory allocation implementation. It's commonly located at '/system/lib64/gralloc.(ro.product.model).so' and usually it takes advantage of the functions that Huawei/Honor implemented in 'libion.so', so you might have to copy it as well.

Right now, the graphics system may work for your device, but if you analyze a logcat from your device, you'll discover that you can implement what We call the "Hardware Composer", this module offloads the graphic system, so adding it will surely be benefitial, it's located at '/system/lib64/hwcomposer.(ro.product.model).so'

In conclusion, the graphics system is based on a kernel driver, a userspace driver (libGLES_mali.so), the memory allocation module (gralloc) and the hardware composer (hwcomposer). It isn't possible to write one guide for every Kirin platform, so should you have any problem, check the GPU specs, maybe you're missing an OpenCL module, logcat will help you there.


PS: As you may have realized, when I make reference to a library, I do it with lib64 instead of lib, I do it because surfaceflinger calls the 64 bit level libraries `readelf -h bin/surfaceflinger`, but for example mediaserver calls the 32 bit libraries (audioflinger for example), make sure to adjust it to your device.
