# Introduction #

The current mainstream release of JavaCL / OpenCL4Java / ScalaCL uses JNA as a way of calling C functions from Java.

However, the author of JavaCL and JNAerator (used to automatically generate JNA wrappers out of C headers) also created an alternative to JNA, designed for better usability, compatibility with C++ and performance : [BridJ](http://code.google.com/p/bridj/).

The promises of BridJ, relative to JavaCL, are :
  * much faster structs
  * much lower low-level calls overhead
  * smaller and leaner low-level bindings
  * better-looking bindings with the JavaCL Generator

# JNA direct mode vs. BridJ #

JNA supports a direct call mode, which is faster than the "normal" (reflection-intensive) mode currently used by JavaCL.

However, JNA's direct mode does not support many constructs that are highly needed by JavaCL (typed pointers, for instance).

BridJ, on the other side, performs all its call in direct mode (direct means that all BridJ functions are declared as "native", and their binding implies dynamic native code generation on the native side).

BridJ also features a "raw" call mode (see [BridJ Design](http://code.google.com/p/bridj/wiki/Design)), which uses optimized assembly thunks so that the resulting performance nears that of hand-written JNI.

# Other benefits of BridJ over JNA for JavaCL #

BridJ's structures were also made to be much faster. JavaCL itself uses very few structs (image\_format...), but using structs defined in OpenCL kernels is very easy to do via the JavaCL Generator.