

# What is it ? #

If you're doing OpenCL stuff from Java, you don't want to miss out on the JavaCL Generator : it integrates your OpenCL code to your Java code seamlessly !

The _JavaCL Generator_ is a Maven plugin that automatically parses any OpenCL source file present in `src/main/opencl` and generates a Java wrapper class that lets you call the kernel very easily from Java.

# Simple example #

## Example OpenCL source file ##

In the rest of this page, we'll consider the following kernels (from the [tutorial](GettingStarted.md)), saved in the file `src/main/opencl/tutorial/TutorialKernels.cl`:
```
__kernel void add_floats(__global const float* a, __global const float* b, __global float* out, int n) 
{
    int i = get_global_id(0);
    if (i >= n)
        return;

    out[i] = a[i] + b[i];
}

__kernel void fill_in_values(__global float* a, __global float* b, int n) 
{
    int i = get_global_id(0);
    if (i >= n)
        return;

    a[i] = cos((float)i);
    b[i] = sin((float)i);
}
```

## Maven configuration for the JavaCL Generator ##

This is the configuration needed in your `pom.xml` to make use of the generator (it adds up to the [JavaCL dependency and NativeLibs4Java repository configuration](http://code.google.com/p/javacl/wiki/Usage#With_Maven)) :
```
<project>
  ...
  <build>
    <plugins>
        ...
      <plugin>
        <groupId>com.nativelibs4java</groupId>
        <artifactId>maven-javacl-plugin</artifactId>
        <version>1.0-SNAPSHOT</version>
        <executions>
        <execution>
          <phase>generate-sources</phase>
          <goals>
            <goal>compile</goal>
          </goals>
        </execution>
        </executions>
      </plugin>
	  
    </plugins>
  </build>
</project>
```

In previous versions (up to `1.0.0-RC1`), the plugin's artifact name was `javacl-generator`. It is now the artifact of a standalone generator which `maven-javacl-plugin` depends upon.

## Generated code ##

At each Maven compilation, the JavaCL Generator plugin will automatically generate the following wrapper as `target/generated-sources/main/java/tutorial/TutorialKernels.java` :
```
package com.mycompany;
import com.nativelibs4java.opencl.CLAbstractUserProgram;
import com.nativelibs4java.opencl.CLBuffer;
import com.nativelibs4java.opencl.CLBuildException;
import com.nativelibs4java.opencl.CLContext;
import com.nativelibs4java.opencl.CLEvent;
import com.nativelibs4java.opencl.CLKernel;
import com.nativelibs4java.opencl.CLProgram;
import com.nativelibs4java.opencl.CLQueue;
import java.io.IOException;
/// Wrapper around the OpenCL program TutorialKernels
public class TutorialKernels extends CLAbstractUserProgram {
	public TutorialKernels(CLContext context) throws IOException {
		super(context, readRawSourceForClass(TutorialKernels.class));
	}
	public TutorialKernels(CLProgram program) throws IOException {
		super(program, readRawSourceForClass(TutorialKernels.class));
	}
	CLKernel add_floats_kernel;
	public synchronized CLEvent add_floats(CLQueue commandQueue, CLBuffer<Float > a, CLBuffer<Float > b, CLBuffer<Float > out, int n, int globalWorkSizes[], int localWorkSizes[], CLEvent... eventsToWaitFor) throws CLBuildException {
		if (add_floats_kernel == null) 
			add_floats_kernel = createKernel("add_floats");
		add_floats_kernel.setArgs(a, b, out, n);
		return add_floats_kernel.enqueueNDRange(commandQueue, globalWorkSizes, localWorkSizes, eventsToWaitFor);
	}
	CLKernel fill_in_values_kernel;
	public synchronized CLEvent fill_in_values(CLQueue commandQueue, CLBuffer<Float > a, CLBuffer<Float > b, int n, int globalWorkSizes[], int localWorkSizes[], CLEvent... eventsToWaitFor) throws CLBuildException {
		if (fill_in_values_kernel == null) 
			fill_in_values_kernel = createKernel("fill_in_values");
		fill_in_values_kernel.setArgs(a, b, n);
		return fill_in_values_kernel.enqueueNDRange(commandQueue, globalWorkSizes, localWorkSizes, eventsToWaitFor);
	}
}
```

# Why using the Generator ? #

## Type-safety of Java-to-OpenCL-kernels calls ##

With the generator, you get **compile-time checks** of the argument types of your kernels, with **complete synchronization with your OpenCL sources**.

The alternative is to call [CLKernel.setArgs(Object...)](http://nativelibs4java.sourceforge.net/javacl/api/stable/com/nativelibs4java/opencl/CLKernel.html#setArgs(java.lang.Object...)) on your kernels prior to enqueuing them for execution, and it's dangerous for you might not be using the correct number of arguments or the correct argument types  : your program will then crash at runtime or just be buggy and make you cry.

Remember why you're using Java/Scala instead of Php/Ruby ? Get the free compile-time checks for OpenCL now ;-)

## Less boilerplate code ##

Compare this (**with** the generator) :
```
TutorialKernels kernels = new TutorialKernels(context);
int[] globalSizes = new int[] { n };
CLEvent fillEvt = kernels.fill_in_values(queue, a, b, n, globalSizes, null);
CLEvent addEvt = kernels.add_floats(queue, a, b, out, n, globalSizes, null, fillEvt);
```

To that (**without** the generator) :
```
// Read the program sources and compile them :
String src = IOUtils.readText(JavaCLTutorial1.class.getResource("TutorialKernels.cl"));
CLProgram program = context.createProgram(src);
CLKernel fillValuesKernel = program.createKernel("fill_in_values");
CLKernel addFloatsKernel = program.createKernel("add_floats");

// Get and call the kernel :
int[] globalSizes = new int[] { n };
fillValuesKernel.setArgs(a, b, n);
CLEvent fillEvt = fillValuesKernel.enqueueNDRange(queue, globalSizes);

addFloatsKernel.setArgs(a, b, out, n);
CLEvent addEvt = addFloatsKernel.enqueueNDRange(queue, globalSizes, fillEvt);
```

# How does it work ? #

Well, it's just a derivative of [JNAerator](http://code.google.com/p/jnaerator/) (which is written by the same [original author](http://ochafik.com/) as JavaCL ;-))


# Legal details (no need to read that) #

The generated Java sources (OpenCL wrappers) are explicitly left free of any copyright/licensing bullshit from nativelibs4java, so you can do whatever you want with them (they mainly reflect your OpenCL code anyway).

The generator itself, however, is currently licensed under the LGPL, as it is a derivative of [JNAerator](http://code.google.com/p/jnaerator/) (it is planned to switch it to the BSD license, [as JavaCL itself](CreditsAndLicense.md), but JNAerator needs to decouple from JNA first so it may take a while...).