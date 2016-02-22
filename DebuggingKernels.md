# GDB + JavaCL guide #

Please read this great blog post from Igor Suhorukov to get started

[OpenCL kernel debugging for java host code](http://suhorukov.blogspot.com/2011/12/opencl-kernel-debugging-for-java-host.html).

# Built-in support in JavaCL #

Following Igor's post, a new "debug" setting was added to JavaCL to ease up debugging :
`JAVACL_DEBUG=1` (or the equivalent Java property `javacl.debug=true`).

It will print out the process id, give hints on how to run gdb and will insert debugging-friendly compiler options into all the kernels (and will force-disable any conflicting optimization option such as `-O3` or `-g:none`) :
  * On MacOS X : `-g`
  * On other Unices and Windows : `-O0 -g` (assuming some llvm derivative is used)
  * One can provide custom debug arguments with the environment variable `JAVACL_DEBUG_COMPILER_FLAGS`