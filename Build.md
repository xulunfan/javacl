

All NativeLibs4Java's sub-projects can be built from [sources](http://code.google.com/p/nativelibs4java/source/browse/) in one step.
This includes :
  * [JavaCL, ScalaCL and OpenCL4Java](http://code.google.com/p/javacl/)
  * [JNAerator](http://code.google.com/p/jnaerator/)
  * [BridJ](http://code.google.com/p/bridj/)
  * [Mono4Java](http://code.google.com/p/nativelibs4java/wiki/Mono)

# Full build (first time) #

This will build all of NativeLibs4Java's projects, including JNAerator.
It will take some time, so you'll want to have a coffee during the build (if you've never used Maven before, this can take up to 15 minutes, so that might be enough for two coffees).

Subsequent builds will be much quicker (see next section), and can be launched from your Maven-aware IDE of choice.

  * First, [install Maven](http://maven.apache.org/download.html).
  * Checkout [nativelibs4java files](https://github.com/ochafik/nativelibs4java/tree/master/libraries/) :
```
git clone git://github.com/ochafik/nativelibs4java.git
cd nativelibs4java/libraries
```
  * Run the following Maven command :
```
mvn install -DskipTests
```

After the build is finished, you'll find JavaCL's full self-contained JAR in `OpenCL/JavaCL/target/javacl-xxx-shaded.jar`

# Incremental builds (after a first full build) #

To build JavaCL Demos, just cd to the libraries/OpenCL/Demos directory and run the following command (works the same for any other sub-project) :
```
mvn clean install
```

To avoid launching tests, you can append a `-DskipTests` argument to that command.

# Regenerating low-level bindings #

This is **not advised**, **not supported** and **reserved to experienced users**.

Depending on the platform you're regenerating the bindings on, you might have to edit the generated files to avoid regressions (use some git diff tool for that). Bindings are routinely regenerated under MacOS X in order to test JNAerator's latest snapshots.

Run the following command in the OpenCL4Java directory, after a first full build :
```
mvn clean install -Pregenerate
```