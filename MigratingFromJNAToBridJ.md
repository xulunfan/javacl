# What's up ? #

JavaCL 1.0-RC and later use [BridJ](http://code.google.com/p/bridj/) as the underlying Java/C interoperability runtime, as opposed to previous 1.0-beta versions that used [JNA](https://github.com/twall/jna).

The JNA-powered version of JavaCL is still available as a separate download (and as a Maven artifact that has the `-jna` suffix, like `javacl-jna` or `javacl-core-jna`), but you are advised to migrate for some key reasons :
  * Lower native call overhead (BridJ beats out JNA in performance)
  * BSD-licensed (the JNA version is LGPL-licensed)
  * Nicer API, with generic `CLBuffer<T>` instead of typed buffer classes (`CLIntBuffer` is now `CLBuffer<Integer>`)
  * All new features will only be introduced into the BridJ version, and some are already specific :
    * the OpenCL-backed [UJMP](http://www.ujmp.org/) matrix implementation (in the `javacl-blas` Maven artifact)
    * [ScalaCL](http://code.google.com/p/scalacl/)

# Changes needed #

As you'll notice by changing the JavaCL JAR or Maven dependency from a 1.0-beta version to a version 1.0-RC or later, there are a few changes to perform in the code to fix the compilation due to the migration from JNA to BridJ :
  * Replace `CLXXXBuffer` by `CLBuffer<XXX>`
  * Replace `XXXBuffer.allocate(int)` and `NIOUtils.directXXXs(int)` by [Pointer](http://nativelibs4java.sourceforge.net/bridj/api/stable/org/bridj/Pointer.html).`allocateXXXs(int)`
  * Replace `XXXBuffer.wrap(xxx[])` by [Pointer](http://nativelibs4java.sourceforge.net/bridj/api/stable/org/bridj/Pointer.html).`pointerToXXXs(xxx[])`
  * Replace `XXXBuffer` by `Pointer<XXX>`
  * Replace `Reductor<XXXBuffer>` by `Reductor<XXX>`
  * Replace `XXXBuffer.equals(...)` by `Pointer<XXX>.compareTo(...) == 0` ([Pointer.equals](http://nativelibs4java.sourceforge.net/bridj/api/stable/org/bridj/Pointer.html#equals(java.lang.Object)) does not compare pointed memory contents as `XXXBuffer.equals` does)
  * Replace `import java.nio.*;` by `import `[org.bridj](http://nativelibs4java.sourceforge.net/bridj/api/stable/org/bridj/package-summary.html)`.*;`

# Mechanical changes #

Migrating large JavaCL code bases by hand can be tedious, and doing it with text replacement can be error-prone (especially with the `Int` vs. `Integer` naming exceptions).

To ease up the migration, we've prepared the regular expressions you'll need to get almost all of the migration done (the syntax below assumes you're using [jEdit](http://www.jedit.org/), but should be easy to adapt).

**Be sure to make a backup of your files and local changes before performing these replacements.**

<table>
<tr><th> Regular Expressions </th><th>  Beanshell replace snippets in jEdit </th></tr>
<tr><td> <code>\bCLIntBuffer\b</code></td><td><code>"CLBuffer&lt;Integer&gt;"</code> </td></tr>
<tr><td> <code>\bCLCharBuffer\b</code></td><td><code>"CLBuffer&lt;Character&gt;"</code> </td></tr>
<tr><td> <code>\bCL(Long||Short||Double||Float||Byte)Buffer\b</code></td><td><code>"CLBuffer&lt;" + _1 + "&gt;"</code> </td></tr>
<tr><td> <code>\b(Int||Char||Long||Short||Double||Float||Byte)Buffer\.allocate\(\b</code></td><td><code>"Pointer.allocate" + _1 + "s("</code> </td></tr>
<tr><td> <code>\bNIOUtils\.direct(Int||Char||Long||Short||Double||Float||Byte)s\(\b</code></td><td><code>"Pointer.allocate" + _1 + "s("</code> </td></tr>
<tr><td> <code>\b(Int||Char||Long||Short||Double||Float||Byte)Buffer\.wrap\(\b</code></td><td><code>"Pointer.pointerTo" + _1 + "s("</code> </td></tr>
<tr><td> <code>\bIntBuffer\b</code></td><td><code>"Pointer&lt;Integer&gt;"</code> </td></tr>
<tr><td> <code>\bCharBuffer\b</code></td><td><code>"Pointer&lt;Character&gt;"</code> </td></tr>
<tr><td> <code>\b(Long||Short||Double||Float||Byte)Buffer\b</code></td><td><code>"Pointer&lt;" + _1 + "&gt;"</code> </td></tr>
<tr><td> <code>\bReductor&lt;(\w*)Buffer&gt;</code></td><td><code>"Reductor&lt;" + _1 + "&gt;"</code> </td></tr>
<tr><td> <code>import java.nio.*;</code></td><td><code>"import org.bridj.*;"</code> </td></tr>
</table>