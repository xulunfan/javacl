

OpenCL supports 2D and 3D images as first-class entities within computation kernels.

# Image Formats #

It's very easy to query an OpenCL implementation for supported image formats using `CLContext.getSupportedImageFormats` :
```
import com.nativelibs4java.opencl.*;

CLContext context = JavaCL.createBestContext();
CLImageFormat[] formats = context.getSupportedImageFormats(CLMem.Flags.ReadWrite, CLMem.ObjectType.Image2D);
for (CLImageFormat format : formats)
    System.out.println("Supported format : " + format);
```

The [OpenCL specification](http://www.khronos.org/registry/cl/sdk/1.0/docs/man/xhtml/clGetSupportedImageFormats.html) guarantees that the following formats are available on all OpenCL implementations :
  * `RGBA, UNormInt8`
  * `RGBA, UNormInt16`
  * `RGBA, SignedInt8`
  * `RGBA, SignedInt16`
  * `RGBA, SignedInt32`
  * `RGBA, UnsignedInt8`
  * `RGBA, UnsignedInt16`
  * `RGBA, UnsignedInt32`
  * `RGBA, HalfFloat`
  * `RGBA, Float`
  * `BGRA, UNormInt8`

This means that an OpenCL implementation may not support any grayscale image type natively, but JavaCL still offers easy ways to handle [BufferedImages](http://download.oracle.com/javase/6/docs/api/java/awt/image/BufferedImage.html) of the `TYPE_BYTE_GRAY` and `TYPE_USHORT_GRAY` types (see below).

# Reading and Writing to CLImage(2D/3D) #

## The easy way : BufferedImage ##

JavaCL supports reading and writing images directly to and from BufferedImage instances, using the methods `CLImage2D.read(CLQueue)` and `CLImage2D.write(CLQueue, BufferedImage)`.

Only the following BufferedImage types are supported :
  * `TYPE_INT_ARGB` : can be written to CLImage2D instances with format :
    * `(BGRA, UNormInt8)`
    * `(RGBA, UNormInt8)`
  * `TYPE_USHORT_GRAY` : can be written to CLImage2D instances with format :
    * `(LUMINANCE, UnsignedInt16)` : this format is not supported by many platforms as of this writing
    * `(RGBA, UNormInt16)` : when writing to the CLImage2D, the luminance value of the BufferedImage is written to the Red, Green and Blue channels while the Alpha channel is given the opaque `0xffff` value. When reading, the luminance is computed by averaging the Red, Green and Blue channels while ignoring the Alpha channel.
  * `TYPE_BYTE_GRAY` : can be written to CLImage2D instances with format :
    * `(LUMINANCE, UnsignedInt8)` : this format is not supported by many platforms as of this writing

## The hard way : direct data ##

One has to know the exact memory layout of the OpenCL image when reading or writing the image data directly.

Please refer to the OpenCL documentation :
  * [cl\_image\_format](http://www.khronos.org/registry/cl/sdk/1.0/docs/man/xhtml/cl_image_format.html)

## Examples ##

Please look at the [ImageTest](https://github.com/ochafik/nativelibs4java/tree/master/libraries/OpenCL/Core/src/test/java/com/nativelibs4java/opencl/ImageTest.java) class.

# Sample Image Transform Program #

Here it is :
```
import com.nativelibs4java.opencl.*;
import java.io.*;
import java.awt.image.*;
import com.ochafik.io.ReadText;

public class ImageSample {
        public static void main(String[] args) throws Exception {
                String inputFile = "input.jpg", outputFile = "output.jpg", kernelFile = "kernel.cl";
                
                CLContext context = JavaCL.createBestContext();
                
                BufferedImage bufferedImage = ImageIO.read(new File(inputFile));
                
                CLQueue queue = context.createDefaultQueue();
                CLProgram program = context.createProgram(ReadText.readText(new File(kernelFile)));
                CLKernel kernel = program.createKernels()[0];
                
                int width = bufferedImage.getWidth(), height = bufferedImage.getHeight(); 
                CLImage2D imageIn = context.createImage2D(CLMem.Usage.Input, bufferedImage, false);
                CLImage2D imageOut = context.createImage2D(CLMem.Usage.Output, imageIn.getFormat(), width, height);
                
                kernel.setArgs(imageIn, imageOut);
                CLEvent evt = kernel.enqueueNDRange(queue, new int[] { width, height });
                
                BufferedImage result = imageOut.read(queue, evt);
                
                ImageIO.write(result, "jpeg", new File(outputFile));
        }
}
```

With `kernel.cl` being any of the samples at the following address :
[JavaCL's Image Transform Kernel Samples](https://github.com/ochafik/nativelibs4java/tree/master/libraries/OpenCL/InteractiveImageDemo/src/main/resources/#resources%2Fexamples)