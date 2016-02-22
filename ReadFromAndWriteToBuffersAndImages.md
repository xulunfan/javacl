

# Introduction #

This page describes common properties and principles of buffers and images, but another page is dedicated to image-specific features : [UsingImages](UsingImages.md).

## OpenCL Memory Objects ##

OpenCL lets you create memory objects of three types : buffers (1D arrays), 2D images and 3D images.

These memory objects can be stored in the host memory (typically, in RAM) or in the device memory (typically, in GRAM directly on the graphic card).

## Memory allocation : host vs. device ##

For host memory-stored memory objects, you can either ask OpenCL to allocate memory on its own or directly providing the pointer to some memory you allocated (and initialized) by yourself.

When using a host-allocated memory object, OpenCL might let the kernels access directly to the data from the host or instead choose to cache some of this data, in read and/or write modes.
What it means is that if you create a direct buffer of values and create an OpenCL buffer out of it, in "use host pointer" mode, you cannot expect your changes done on your direct buffer to be visible from OpenCL kernels using the OpenCL buffer. Before and after doing any change to or reading any data from a host pointer, you must call the CLBuffer.map / unmap methods.

Lastly, OpenCL might let you read/write directly from an OpenCL memory object with the map/unmap mechanism.
Mapping a memory object gives a direct pointer to the memory object's data (for instance using a DMA mechanism).
However, mapping a memory object's data is only guaranteed to work in the case of host-allocated pointers, so it should be used with caution.

# Creating Memory Objects with JavaCL #

## Creating Buffers ##

While OpenCL defines buffers as arrays of bytes, JavaCL provides support for typed buffers with 1 to 1 mapping to NIO buffers.

If you use a `__global float*` argument in a kernel of yours, you can either choose to use a `CLBuffer<Byte>` and do the `float <-> byte` conversion and offsets calculations on your own or directly use a `CLBuffer<Float>` that will take care of everything.

### Host-allocated pointers ###

<table><tr><th>JavaCL / BridJ</th><th>JavaCL / JNA</th></tr><tr><td>
<pre><code>CLContext context = ... ;<br>
<br>
int size = 100;<br>
Pointer&lt;Float&gt; ptr = Pointer.allocateFloats(size).order(context.getByteOrder());<br>
<br>
// Last 'false' argument requires that no copy of the pointer is made : <br>
// keep ptr as the primary data source<br>
CLBuffer&lt;Float&gt; clBuffer = context.createBuffer(CLMem.Usage.InputOutput, ptr, false);<br>
</code></pre>
</td><td>
<pre><code>CLContext context = ... ;<br>
<br>
int size = 100;<br>
FloatBuffer ptr = ByteBuffer.allocateDirect(size * 4).byteOrder(context.getByteOrder()).asFloatBuffer();<br>
<br>
// Last 'false' argument requires that no copy of the pointer is made : <br>
// keep ptr as the primary data source<br>
CLBuffer&lt;FloatBuffer&gt; clBuffer = context.createBuffer(CLMem.Usage.InputOutput, ptr, false);<br>
</code></pre>
</td></tr></table>

### Device-allocated pointers ###

Allocate a zeroed-out buffer :
```
int size = 100;
CLBuffer<Float> buffer = context.createFloatBuffer(CLMem.Usage.InputOutput, size);
```

Allocate a buffer as a copy of existing data :
```
// Last 'true' argument asks for a copy of ptr to be made in the device memory. 
// The clBuffer will have no further link to ptr.
CLBuffer<Float> clBuffer = context.createBuffer(CLMem.Usage.InputOutput, ptr, true);
```

# Copying memory around #

## Reading from / Writing to a buffer ##

```
CLContext context = ... ; // e.g. JavaCL.getBestContext()
CLQueue queue = ... ; // e.g. context.createDefaultQueue()
CLBuffer<Float> clBuffer = ... ; // e.g. context.createFloatBuffer(CLMem.Usage.InputOutput, size)
```
<table><tr><th>JavaCL / BridJ</th><th>JavaCL / JNA</th></tr><tr><td>
<pre><code>// Reading everything to a new native memory location :<br>
Pointer&lt;Float&gt; data = clBuffer.read(queue);<br>
<br>
// Reading elements 3 to 9 included to a new native memory location :<br>
data = clBuffer.read(queue, 3, 7);<br>
<br>
// Reading elements 3 to 9 included to existing data memory location :<br>
data = Pointer.allocateFloats(7).order(context.getByteOrder());<br>
clBuffer.read(queue, 3, 7, data, true); // 'true' : blocking operation<br>
</code></pre>
</td><td>
<pre><code>// Reading everything to a new buffer :<br>
FloatBuffer data = clBuffer.read(queue);<br>
<br>
// Reading elements 3 to 9 included to a new buffer :<br>
data = clBuffer.read(queue, 3, 7);<br>
<br>
// Reading elements 3 to 9 included to existing data buffer :<br>
// (note that while indirect buffers will work, direct buffers will provide much better performance)<br>
data = NIOUtils.directFloats(7, context.getByteOrder());<br>
clBuffer.read(queue, 3, 7, data, true); // 'true' : blocking operation<br>
</code></pre>
</td></tr></table>

## Mapping a buffer for read / write operations ##

Mapping a buffer is only guaranteed to work for buffers allocated from host-pointers.

<table><tr><th>JavaCL / BridJ</th><th>JavaCL / JNA</th></tr><tr><td>
<pre><code>CLBuffer&lt;Float&gt; clBuffer = ... ; <br>
<br>
try {<br>
  Pointer&lt;Float&gt; data = clBuffer.map(queue. CLMem.MapFlags.Write);<br>
  data.set(10f); // write something to the buffer<br>
  clBuffer.unmap(queue, data);<br>
} catch (CLException.MapFailure ex) {<br>
  // map didn't succeed : maybe use CLBuffer.write / read instead.<br>
}<br>
</code></pre>
</td><td>
<pre><code>CLBuffer&lt;Float&gt; clBuffer = ... ; <br>
<br>
try {<br>
  FloatBuffer data = clBuffer.map(queue. CLMem.MapFlags.Write);<br>
  data.put(0, 10f); // write something to the buffer<br>
  clBuffer.unmap(queue, data);<br>
} catch (CLException.MapFailure ex) {<br>
  // map didn't succeed : maybe use CLBuffer.write / read instead.<br>
}<br>
</code></pre>
</td></tr></table>

## Copying buffers and images ##

Copying a buffer to another one:
```
CLBuffer<Float> original = ... ;
CLBuffer<Float> copy = context.createFloatBuffer(CLMem.Usage.Input, original.getElementCount());
CLEvent e = original.copyTo(queue, copy);
```

Copying an image to another one:
```
CLImage2D original = ... ;
CLImage2D copy = context.createImage2D(CLMem.Usage.Input, original.getWidth(), original.getHeight());
CLEvent e = original.copyTo(queue, copy);
```

# Important Note on Endianness #

Each OpenCL device can have a different [endianness](http://en.wikipedia.org/wiki/Endianness).

This makes it very hard to share the same buffers between two devices with mismatching endianness (for instance, take a little endian Intel Core device vs. a big endian Radeon 4850  GPU device on the same ATI Stream platform).

CLContext.getByteOrder() can be called to check that the context has a consistent byte ordering (checks that all of its device's CLDevice.getByteOrder() methods return the same order, and returns null in case it's mismatching).

All the CLBuffer read/map methods should return properly ordered NIO buffers or BridJ pointers (using the order of the queue's device).

NIO Buffers / BridJ Pointers given to all CLBuffer write methods should be ordered with the correct byte order (see examples above).



# Garbage Collection vs. Manual Release #

JavaCL maps OpenCL entities (allocated by the OpenCL driver, typically in the device memory) to Java objects (managed by the JVM's garbage collector).

OpenCL entities are released when their Java object counterparts are garbage collected or when their release() method is called.

In many cases, waiting for the GC to do the work can lead to serious issues : when the OpenCL driver runs out of memory, it does not tell Java to try and collect unused objects (which would release a few OpenCL entities in the process) and just fails, which makes JavaCL throw a `CLException.MemObjectAllocationFailure` or `CLException.OutOfResources` exception.

To avoid that, one can manually release an unused buffer (or any JavaCL entity) by calling `CLAbstractEntity.release()` (`CLAbstractEntity` is a base class which is inherited by `CLBuffer`, `CLImage2D`, `CLProgram`, `CLEvent`... virtually all JavaCL classes of interest).

Fortunately, JavaCL features a workaround for allocations : whenever they fail by lack of OpenCL memory, JavaCL triggers a full GC, waits a little while and retries. This might have a terribly negative impact on your application's performance, though, so please call `release()` as soon as you can!