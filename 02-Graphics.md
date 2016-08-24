#Graphics

The graphics system is going to be tough to implement, since pretty much every component is proprietary.

##Overview

Kirin SoCs graphic system is powered by a Mali GPU, (owned by ARM) and it takes advantage of Open GL (some support OpenCL, depends on the GPU)

## Structure

Everything starts at the Surfaceflinger binary, that calls Android's GL implementation, also called libEGL. libEGL will take care of everything,it determines if there's a GPU and will look for the drivers.

If you take a look at libEGL's source, you'll find a file called Loader.cpp, it will look for your drivers at `/{system|vendor}/lib64/egl`and it's usually called libGLES_mali.so. It will communicate with the kernel mldule and talk to your GPU. Everything looks fine so far!

##Userspace patching

However chances are it isn't, let's go step by step: The first library loaded, libEGL, probably contains some proprietary code by Huawei. You'll figure it out if you use the libEGL present in EMUI, because it will request additional libraries. But don't start by replacing it.

Surfaceflinger will also look for a couple of modules called Gralloc and HwComposer. It's mandatory to include Gralloc since it's the responsible of managing memory for the graphics system, however you can leave out HwComposer.

But why would you do that? Because these modules are proprietary, and Huawei's HwComposer implementation might not be compatible with every rom.

##Conclusion

The graphic system is so complicated and private that it's hard to write a generic guide. You'll have to fight against proprietary libraries and missing dependencies.

I wish you luck!
