# Requirements #

JavaCL supports any platform supported by JNA with a Khronos-compliant OpenCL implementation.

In practise this means any OpenCL platform (Linux, Windows, MacOS X and so on)

  * Macintosh : [MacOS X 10.6 Snow Leopard](http://www.apple.com/macosx/) (CPU and/or any supported GPU)
  * Other systems :
    * NVIDIA GPU : [beta OpenCL-enabled drivers](http://www.nvidia.com/object/cuda_opencl.html)
    * ATI GPU or _any SSE3-capable CPU_ (Windows, Linux) : [ATI's Stream SDK beta 4](http://developer.amd.com/GPU/ATISTREAMSDKBETAPROGRAM/Pages/default.aspx)

JavaCL is routinely tested on :
  * MacOS X Snow Leopard
  * Windows + ATI Stream 2 beta 3 and 4 (32 bits)
  * Windows + NVidia beta drivers (versions 190.38 to 195.62)