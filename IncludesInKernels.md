

# Generalities on C includes #
In C (and in OpenCL's C dialect), it is possible to include C files into a source file.

This is done by the C preprocessor and requires a list of include paths, which usually contain the current directory of the process and is usually passed to the compiler with the `-Ipath` command-line syntax.

# Includes in JavaCL #

JavaCL provides two ways to include files easily from your OpenCL kernels :
  * Any Java resource can be included without any further work. Say the file `MyCLLib/someHeader.h` is visible in the classpath (maybe inside a JAR, for instance), then the `#include "MyCLLib/someHeader.h` statement in any OpenCL kernel passed to JavaCL will just work as expected !
  * `CLProgram.addInclude(path)` adds a directory or an URL to the list of include paths

# LibCL : ready-to-use includes #

Additionally, the JavaCL Project contains a new library called LibCL, which [gathers useful includes for OpenCL kernels](https://github.com/ochafik/nativelibs4java/tree/master/libraries/OpenCL/LibCL/#LibCL%2Fsrc%2Fmain%2Fresources%2FLibCL). It is bundled as a plain old JAR and contains resources that are includable using JavaCL's classpath inclusion mechanism.

If you use Maven, you just need to add the following dependency to get started :
```
	<dependency>
	  <groupId>com.nativelibs4java</groupId>
	  <artifactId>libcl-bridj</artifactId>
	  <version>1.0-SNAPSHOT</version>
	</dependency>
```

You can then include any of [LibCL's files](https://github.com/ochafik/nativelibs4java/tree/master/libraries/OpenCL/LibCL/#LibCL%2Fsrc%2Fmain%2Fresources%2FLibCL) in your kernels, say RGB `<->` HSL conversion functions, for instance :
```
#include "LibCL/rgba2hsla.cl"
#include "LibCL/hsla2rgba.cl"

__kernel void testKernel(
        read_only image2d_t inputImage,
        write_only image2d_t outputImage)
{
        int x = get_global_id(0), y = get_global_id(1);

        const sampler_t sampler = CLK_NORMALIZED_COORDS_FALSE | CLK_FILTER_NEAREST | CLK_ADDRESS_CLAMP_TO_EDGE;
        float4 pixel = read_imagef(inputImage, sampler, (int2)(x, y));

        float4 pixelHSLA = rgba2hsla(pixel);
        pixelHSLA.y /= 2; // desaturate pixel.
        pixel = hsla2rgba(pixelHSLA);
        write_imagef(outputImage, (int2)(x, y), pixel);
}
```