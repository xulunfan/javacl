There are a few parameters which can be supplied to JavaCL when executing your program. You can specify these either by setting the corresponding Java system properties (the -D argument to the java executable) to `true` or `false` or by defining the corresponding environment variables as 0 or 1.

| **Java property** | **Environment variable** | **Description** | **Default** |
|:------------------|:-------------------------|:----------------|:------------|
| javacl.cacheBinaries | JAVACL\_CACHE\_BINARIES  | If enabled, JavaCL saves the compiled binaries of the OpenCL programs to a temporary folder, and uses these binaries when the same program is needed in the future, avoiding multiple compilations. | Enabled, except on AMD systems (see [Issue 39](http://code.google.com/p/nativelibs4java/issues/detail?id=39)) |
| javacl.adjustDoubleExtension | JAVACL\_ADJUST\_DOUBLE\_EXTENSION | Allows JavaCL to change the double extension used by the kernel between `cl_khr_fp64` and `cl_amd_fp64` if one is supported and the other is not. | Enabled     |
| javacl.verbose    | JAVACL\_VERBOSE          | Enables logging of additional messages. If you enable it, you should also enable Java assertions. | Disabled    |

Example:

```
java -Djavacl.cacheBinaries=false -jar yourprogram.jar

# On Bourne-style shells:
JAVACL_ADJUST_DOUBLE_EXTENSION=0 java -jar yourprogram.jar
# On Windows:
set JAVACL_VERBOSE=1 java -ea -jar yourprogram.jar
```