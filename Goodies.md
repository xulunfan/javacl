

# Fourier Transforms (DFT, power-of-two FFT) #

JavaCL ships with ready-to-use [FFT](http://en.wikipedia.org/wiki/Fast_Fourier_transform) utility classes (Fourier transform and inverse transform) :
  * [FloatFFTPow2](http://nativelibs4java.sourceforge.net/javacl/api/stable/com/nativelibs4java/opencl/util/fft/FloatFFTPow2.html)
  * [DoubleFFTPow2](http://nativelibs4java.sourceforge.net/javacl/api/stable/com/nativelibs4java/opencl/util/fft/DoubleFFTPow2.html)

It is pretty similar to Apache Common's [FastFourierTransformer](http://commons.apache.org/math/api-2.1/org/apache/commons/math/transform/FastFourierTransformer.html) class, except that :
  * It's **significantly faster** (even when run on a CPU OpenCL device)
  * It has floats and doubles variants (Apache's implementation only deals with doubles)
  * It uses primitive arrays instead of arrays of [Complex](http://commons.apache.org/math/api-2.1/org/apache/commons/math/complex/Complex.html) for complex data (it interleaves real and imaginary values in an array that's twice as big)

JavaCL even features a **slower** general Fourier Transform that works with input sizes that aren't powers of two :
  * [FloatDFT](http://nativelibs4java.sourceforge.net/javacl/api/stable/com/nativelibs4java/opencl/util/fft/FloatDFT.html)
  * [DoubleDFT](http://nativelibs4java.sourceforge.net/javacl/api/stable/com/nativelibs4java/opencl/util/fft/DoubleDFT.html)

The following code computes an FFT and the reverse FFT in a few lines :
```
int n = 1024; // power-of-two
float[] complexInput = new float[2 * n]; // interleaved real and imag values
for (int i = 0; i < n; i++) {
    complexInput[i * 2] = i; // pure-real input
}
CLContext context = JavaCL.createBestContext(); // use createBestContext(DoubleSupport) for doubles
CLQueue queue = context.createDefaultQueue();

FloatFFTPow2 fft = new FloatFFTPow2(context); // use default OpenCL context
float[] transformed = fft.transform(queue, complexInput, false);
float[] transformedBack = fft.transform(queue, transformed, true);
```

These FFT classes can also operate directly on user-provided input and output [CLBuffer](http://nativelibs4java.sourceforge.net/javacl/api/stable/com/nativelibs4java/opencl/CLBuffer.html) instances, to avoid back-and-forth conversions from Java primitive arrays to OpenCL memory (useful if you're trying to do the FFT of data that was just transformed by OpenCL).

# Parallel Random Numbers Generator #

JavaCL ships with a [Xorshift](http://en.wikipedia.org/wiki/Xorshift) random numbers generator, adapted to work in parallel (using a warmup phase).

[ParallelRandom](http://nativelibs4java.sourceforge.net/javacl/api/stable/com/nativelibs4java/opencl/util/ParallelRandom.html) was designed as a drop-in replacement for [java.util.Random](http://download.oracle.com/javase/6/docs/api/java/util/Random.html) (albeit with a more limited API) :
```
ParallelRandom r = new ParallelRandom();
while (true) {
    System.out.println(r.nextDouble());
}
```

# Parallel Reduction (min, max, sum, product) #

It's very easy to perform many parallel operations in OpenCL, but exploiting its parallel horse-power to aggregate the results is a bit harder.

JavaCL ships with the [ReductionUtils](http://nativelibs4java.sourceforge.net/javacl/api/stable/com/nativelibs4java/opencl/util/ReductionUtils.html) class, which aggregates OpenCL data using simple associative and symmetrical operations :
  * min
  * max
  * sum
  * product

If you have a vector of float2 and need to compute the min of all its values, separating the x and y components, then you can even ask for a 2-channels reductor ! (reduction takes place independently on each channel)

Here's how :
```
CLContext context = JavaCL.createBestContext();
CLQueue queue = context.createDefaultQueue();
// Create a one-channel int "min" reductor :
Reductor<Integer> reductor = ReductionUtils.createReductor(context, ReductionUtils.Operation.Min, OpenCLType.Int, 1);

CLBuffer<Integer> input = context.createIntBuffer(Usage.Input, 1024);
// < fill input with some data here... >

int value = reductor.reduce(queue, input).get();
```

# UJMP Matrices #

The [Universal Java Matrix Package (UJMP)](http://ujmp.org) library is an amazing project that provides a comprehensive API that covers most if not all linear algebra needs, with default implementations as well as wrapper implementations for all existing prominent Java matrix packages on Earth (give a look at its [features list](http://www.ujmp.org/java-matrix/feature-list/), it's just mind-bloggling !).

## Maven JavaCL Blas dependency ##

JavaCL's "javacl-blas" Maven package contains a lazy implementation of float and double 2D dense [Matrix](http://www.ujmp.org/apidocs/ujmp-core/apidocs/org/ujmp/core/Matrix.html) for the [UJMP](UJMP.md) library.
```
<project>
  ...
  <repositories>
    ...
    <repository>
      <id>nativelibs4java</id>
      <name>nativelibs4java Maven2 Repository</name>
      <url>http://nativelibs4java.sourceforge.net/maven</url>
    </repository>
  </repositories>

  <dependencies>
    <dependency>
      <groupId>com.nativelibs4java</groupId>
      <artifactId>javacl-blas</artifactId>
      <version>1.0.0-RC2</version>
    </dependency>
  </dependencies>
  ...
</project>
```

## Transparently asynchronous operations ##

[CLDenseDoubleMatrix2D](http://nativelibs4java.sourceforge.net/javacl/api/stable/com/nativelibs4java/opencl/blas/ujmp/CLDenseDoubleMatrix2D.html) and [CLDenseFloatMatrix2D](http://nativelibs4java.sourceforge.net/javacl/api/stable/com/nativelibs4java/opencl/blas/ujmp/CLDenseFloatMatrix2D.html) feature transparent asynchronicity, powered by the OpenCL event model under the hood. The statement :
```
Matrix abta = a.times(b).times(a.transpose());
```
Returns a matrix, `abta`, that's probably not finished yet. Any attempt to read a specific component of that matrix will wait for all its write computations to finish.

## Accelerated matrix operations ##

  * times (matrix multiplication)
  * transpose (matrix transpose)
  * plus, minus, divide, mtimes (piece-wise binary operators)
  * min, max, center, copy
  * sin, cos, tan, sinh, cosh, tanh, asin, acos, atan, asinh, acosh, atanh
  * containsDouble
  * toDoubleArray
  * clear

## Usage ##

To use OpenCL for all double and float 2D dense matrices :
```
MatrixMapper.getInstance().setDenseDoubleMatrix2DClass(CLDenseDoubleMatrix2D.class);
MatrixMapper.getInstance().setDenseFloatMatrix2DClass(CLDenseFloatMatrix2D.class);
```
You will then be able to create OpenCL-powered matrices with :
```
Matrix m = MatrixFactory.dense(10, 10);
```

Or you can instantiate your matrices with the direct type :
```
CLDenseDoubleMatrix2D 
    a = new CLDenseDoubleMatrix2D(10),
    b = new CLDenseDoubleMatrix2D(10);

Matrix c = a.times(b);
```