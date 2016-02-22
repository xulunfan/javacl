

# Mapping OpenCL C types to JavaCL #

Here are the different kinds of values accepted as kernel arguments in [CLKernel.setArgs](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLKernel.html#setArgs(java.lang.Object...))

## Scalar data types ##

| **[OpenCL C type](http://www.khronos.org/registry/cl/sdk/1.0/docs/man/xhtml/scalarDataTypes.html)** | **JavaCL type** |
|:----------------------------------------------------------------------------------------------------|:----------------|
| int/uint                                                                                            | int             |
| long/ulong                                                                                          | long            |
| short/ushort                                                                                        | short           |
| char/uchar                                                                                          | byte            |
| float                                                                                               | float           |
| double                                                                                              | double          |
| size\_t, ptrdiff\_t, intptr\_t, uintptr\_t                                                          | [NativeSize](http://jnaerator.sourceforge.net/sites/jnaerator-runtime/apidocs/com/ochafik/lang/jnaerator/runtime/NativeSize.html) |

## Vector data types ##
| **[OpenCL C type](http://www.khronos.org/registry/cl/sdk/1.0/docs/man/xhtml/vectorDataTypes.html)** | **JavaCL type** |
|:----------------------------------------------------------------------------------------------------|:----------------|
| int2, int3, int4, int8...                                                                           | int`[]`         |

## Array/Buffer types (including arrays of vector types) ##

Please also read [ReadFromAndWriteToBuffersAndImages](ReadFromAndWriteToBuffersAndImages.md).

| **OpenCL C type** | **JavaCL type** |
|:------------------|:----------------|
| global int`*`<br />global uint`*`<br />global int2`*`<br />... | [CLBuffer&lt;Integer&gt;](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLBuffer.html) |
| global long`*`    | [CLBuffer&lt;Long&gt;](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLBuffer.html)  |
| global short`*`   | [CLBuffer&lt;Short&gt;](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLBuffer.html)  |
| global char`*`    | [CLBuffer&lt;Byte&gt;](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLBuffer.html)  |
| global float`*`   | [CLBuffer&lt;Float&gt;](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLBuffer.html)  |
| global double`*`  | [CLBuffer&lt;Double&gt;](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLBuffer.html)  |

## Image-related types ##

Please also read [ReadFromAndWriteToBuffersAndImages](ReadFromAndWriteToBuffersAndImages.md).

| **OpenCL C type** | **JavaCL type** |
|:------------------|:----------------|
| [sampler\_t](http://www.khronos.org/registry/cl/sdk/1.0/docs/man/xhtml/sampler_t.html) | [CLSampler](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLSampler.html) |
| image2d\_t        | [CLImage2D](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLImage2D.html) |
| image3d\_t        | [CLImage3D](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLImage3D.html) |

## Local Arguments ##

Their memory storage is shared by all the work items in the same work group

| **OpenCL C type** | **JavaCL type** |
|:------------------|:----------------|
| local int`*`      | [LocalSize](http://nativelibs4java.sourceforge.net/sites/javacl/apidocs/com/nativelibs4java/opencl/CLKernel.LocalSize.html) : Mere indication of the size of a local argument |