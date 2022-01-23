### lddtree issue

`ldd` and `lddtree` results differ when modifying the libraries rpath.

This repo is a reproducible case of this issue.


#### Steps

First modify the rpath of a lib:

```shell
patchelf *.so --set-rpath 'somethingwrong/'
```

Then notice the difference between the 2 outputs:

```shell
$ ldd Main                                                                                                                                                                       !10084
	linux-vdso.so.1 (0x00007ffcfabf7000)
	libLibA.so => /home/gogoprog/code/lddtreebug/build/libLibA.so (0x00007f45217fc000)
	libstdc++.so.6 => /usr/lib/libstdc++.so.6 (0x00007f45215bc000)
	libm.so.6 => /usr/lib/libm.so.6 (0x00007f4521478000)
	libgcc_s.so.1 => /usr/lib/libgcc_s.so.1 (0x00007f452145d000)
	libc.so.6 => /usr/lib/libc.so.6 (0x00007f4521291000)
	/lib64/ld-linux-x86-64.so.2 => /usr/lib64/ld-linux-x86-64.so.2 (0x00007f4521808000)
	libLibB.so => not found

```

```shell
$ lddtree Main                                                                                                                                                                   !10085
Main (interpreter => /lib64/ld-linux-x86-64.so.2)
    libLibA.so => /home/gogoprog/code/lddtreebug/build/libLibA.so
        libLibB.so => /home/gogoprog/code/lddtreebug/build/libLibB.so
    libstdc++.so.6 => /usr/lib/libstdc++.so.6
    libm.so.6 => /usr/lib/libm.so.6
    libgcc_s.so.1 => /usr/lib/libgcc_s.so.1
    libc.so.6 => /usr/lib/libc.so.6
```

The depending library `libB` is found with `lddtree` but should not.

It seems that `rpath` info is not used with `lddtree`.


