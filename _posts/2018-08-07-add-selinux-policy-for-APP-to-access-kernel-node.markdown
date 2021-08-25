---
layout: post
title: Add selinux policy for APP to access kernel node
date: 2018-08-07 08:00:00 +0800
description: # Add post description (optional)
img: # Add image post (optional)
categories: Android
tags: [Android]# add tag
---
## Change mode of kernel node
The first thing we need to do is change mode of kernel node, suppose:
* /sys/device/my_node/enable

add bellowing to init.xxx.rc
* chmod 0666 /sys/device/my_node/enable

## Add selinux policy
* define a type in file.te
`type sysfs_mynode, sysfs_type, fs_type, mlstrustedobject;`

* define path in file_contexts
`/sys/devices/my_node/enable	u:object_r:sysfs_mynode:s0`

* give 'shell' permission
```
userdebug_or_eng(`
	allow shell sysfs_mynode:file rw_file_perms;
')
```

* give "untrusted app" permission
`allow untrusted_app sysfs_mynode:file rw_file_perms;`

Now, untrusted app(third party) have full permission(open, read, write) of /sys/device/my_node/enable

## Analyze prograss
* Find avc denied log and add it to output.txt
```
08-02 08:09:24.689  3687  3687 W eng.ar8020_test: type=1400 audit(0.0:35): avc: denied { write } for name="enable" dev="sysfs" ino=35487 scontext=u:r:untrusted_app:s0:c512,c768 tcontext=u:object_r:sysfs_ar8020:s0 tclass=file permissive=0
```

* using audit2allow to generate selinux policy
```
audio2allow -i output.txt
output:
#============= untrusted_app ==============
allow untrusted_app sysfs_ar8020:file write;
```

* The key point is **mlstrustedobject**
`type sysfs_mynode, sysfs_type, fs_type, mlstrustedobject;`

Why need add 'mlstrustedobject'?
"u:r:untrusted_app:s0:c512,c768" means untrusted app is running with categories(c512, c768)
> In 6.0, apps and their data files are assigned an automatically
generated category set (c512,c768 above) derived from their user ID in
order to isolate the processes and files of different users from each
other.
> MLS constraints on read and write, which will
generally allow read access and prohibit write access (unless the file
type is a mlstrustedobject).


