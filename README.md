This is a simple example of doing graphics without X11 on the pi4. it is a simplied version of the well known triangle[_rpi4].c with some additional improvements for robustness. 

## Raspberry Pi 4

The raspberry Pi 4, at the moment of writing this, has a limited KMS driver, and a buggy "fake" kms driver. This is because the GPU is different from the previous ones. Instead of using the `vc` libraries, you will need to use the DRM/GBM.

**What do I need?**

You need a GCC compiler, GDM, EGL, and GLES libraries. The GCC compiler is already included in the Raspbian image. To install the other libraries, simply run:

```bash
sudo apt install libegl1-mesa-dev libgbm-dev libgles2-mesa-dev libdrm-dev
```

You will also need to connect your Raspberry Pi to a screen. The boot config from `/boot/config.txt` that I have used for my tests, if it helps in any way:

```bash
dtoverlay=vc4-fkms-v3d
max_framebuffers=2
hdmi_force_hotplug=1
hdmi_group=2
hdmi_mode=81
```
It DOES work on Pi 4 when using (default) dtoverlay=vc4-kms-v3d instead of dtoverlay=vc4-fkms-v3d .

**How do I try it?**

Copy or download the `triangle_rpi4.c` file onto your Raspberry Pi. Using any terminal, write the following commands to compile the source file:

```
gcc -o triangle_rpi4 triangle_rpi4.c -ldrm -lgbm -lEGL -lGLESv2 -I/usr/include/libdrm -I/usr/include/GLES2
```

To run the executable, type the following:

```
./triangle_rpi4
```

You should see the following output:

```
/dev/dri/card0 not supporting DRM, trying card1, resolution: 3840x2160
Initialized EGL version: 1.4
GL Viewport size: 3840x2160

```

The program will briefly show the following full screen:

![Screenshot of a purple triangle](output.png "Screenshot of a purple triangle")


## Troubleshooting and Questions

**Failed to get EGL version! Error:**

Your EGL might be faulty! Make sure you are using the libraries provided by Raspbian and not the ones installed through apt-get or other package managers. Use the ones in the `/opt/vc/lib` folder. If that does not work, try reinstalling your Raspberry Pi OS.

**I get "Error! The glViewport/glGetIntegerv are not working! EGL might be faulty!" What should I do?**

Same as above.

**How do I change the pixelbuffer resolution?**

Find `pbufferAttribs` and change `EGL_WIDTH` and `EGL_HEIGHT`.

**I get "undefined reference" on some gl functions!**

Some GL functions may not come from GLES library. You may need to get GL1 library by executing `sudo apt-get install libgl1-mesa-dev` and then simply add: `-lGL` flag to the linker, so you get: `gcc -o triangle triangle.o -lbrcmEGL -lbrcmGLESv2 -lGL -L/opt/vc/lib`

**How do I change the buffer sample size or the depth buffer size or the stencil size?**

Find `configAttribs` at the top of the `triangle.c` file and modify the attributes you need. All config attributes are located here <https://www.khronos.org/registry/EGL/sdk/docs/man/html/eglChooseConfig.xhtml>

**Can I use glBegin() and glEnd()?**

You should not, but you can. However, it seems that OpenGL ES does not like that and nothing will be rendered. For this you will need to link the `GL` library as well. Simply, add `-lGL` to the gcc command.


## License

Do whatever you want.

```
This is free and unencumbered software released into the public domain.

Anyone is free to copy, modify, publish, use, compile, sell, or
distribute this software, either in source code form or as a compiled
binary, for any purpose, commercial or non-commercial, and by any
means.

In jurisdictions that recognize copyright laws, the author or authors
of this software dedicate any and all copyright interest in the
software to the public domain. We make this dedication for the benefit
of the public at large and to the detriment of our heirs and
successors. We intend this dedication to be an overt act of
relinquishment in perpetuity of all present and future rights to this
software under copyright law.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR
OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE,
ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.

For more information, please refer to <http://unlicense.org/>
```
