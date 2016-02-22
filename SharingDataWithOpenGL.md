

# Creating an OpenGL-compatible context #

When rendering OpenGL scenes which data comes from OpenCL computations (vertices, colors, norms and/or textures), it is important to make sure that as little data is copied as possible.

OpenCL supports an optional OpenGL interoperability, which starts to be implemented by some graphic card vendors.

Setting up an OpenGL-compatible OpenCL context with JavaCL is easy, but as of this writing the resulting context can very instable, depending on the version of your driver. Only experience will tell you if you really want to enable the interoperability.

```
CLContext context = JavaCL.createContextFromCurrentGL();
```

Yup, that's it.

# What can be shared #

## Vertex Buffer Objects (VBO) ##

They'll be visible to OpenCL as `CLBuffer<Byte>` instances (see [CLContext.createBufferFromGLBuffer(...)](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLContext.html#createBufferFromGLBuffer(com.nativelibs4java.opencl.CLMem.Usage,%20int)))
```
int vbo = ... ;
CLBuffer<Byte> buffer = context.createBufferFromGLBuffer(usage, vbo);
```

## Render Buffers ##
See [CLContext.createImage2DFromGLRenderBuffer](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLContext.html#createImage2DFromGLRenderBuffer(com.nativelibs4java.opencl.CLMem.Usage,%20int))

## 2D Textures ##
See [CLContext.createImage2DFromGLTexture2D](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLContext.html#createImage2DFromGLTexture2D(com.nativelibs4java.opencl.CLMem.Usage,%20com.nativelibs4java.opencl.CLContext.GLTextureTarget,%20int,%20int))

## 3D Textures ##
See [CLContext. createImage3DFromGLTexture3D](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLContext.html#createImage3DFromGLTexture3D(com.nativelibs4java.opencl.CLMem.Usage,%20int,%20int))