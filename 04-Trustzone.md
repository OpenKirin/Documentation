# Trustzone

The trustzone is the part of the SoC where the "secure world operations" take place, such as encryption/decryption and key generetion.

## Overview

The Trustzone present in Huawei Kirin powered phones is developed by Huawei, it looks derivated from the SierraTEE project, but it has gone through some major modifications.

A basic Trustzone operation consists basically on:

1. Open the session
2. Send the command(s)
3. Close the session


## Userspace implementation

In userspace, we have libteec and teecd. Teecd is in charge of opening every session, every other operation is done by whoever sends the petition.

Teecd communicates with the kernelspace driver via ioctls, and it's closed source as well as libteec (altough the original implementation is available).

## Kernelspace implementation

Located in 'drivers/hisi/tzdriver', it checks that every petition is valid and communicates with the Trustzone.

## Sending our own petition

Sending our own petition to the Trustzone isn't as easy as it might be in a Qualcomm platform or as someone who worked with OPTEE might think.

The access to the Trustzone is highly restrictive, first of all, teecd checks the name of the process who requests access against a hardcoded list, this can be overridden, letting your own process to access the Trustzone, but that would just let you through userspace to the kernel, where further checks are performed.

First of all, the process name is checked and then its memory pages are retrieved, mapped and hashed using SHA, then they are compared to a hardcoded value stored inside '/vendor/etc/native_packages.xml', which is sent by teecd to the Trustzone in the first place.

It could look temptating to edit that value inside native_packages but at the end of the file there's a RSA signature of itself, making it impossible to alter.

Once your petition reaches the Trustzone, the process name is checked once again, and sometimes a 4 byte key is also requied (check tc_client_call).

So to sum up, if you want to have access to the Trustzone you should patch the following things:

* Add your process name to the teecd list or change its name pretending to be another one with access.
* Disable name checking in the kernel and hardcode the SHA hash of another process, then set it whenever your process makes an attempt to open the session like:
`if (!strcmp(dev_file->pkg_name, "/system/dev/yourprocess")
	memcpy(g_ca_auth_buf, findit, 256);
`
* Do the same with the key if needed
* Set the process name to make your petition look valid (you can use prctl in order to do this)

## That's all

This was the result of weeks of research and revers eengineering, finding actual flaws inside the Trustzone wasn't in the scope of this writeup. If you are interested in the Trustzone inner code, know that the image is encrypted. The key is probably AES and is located in oeminfo but I didn't bother to decrypt it since it wasn't really needed to achieve what I wanted.
