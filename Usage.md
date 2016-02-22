

# Prequisites #

## OpenCL implementation ##

There are now quite a few [OpenCL](http://www.khronos.org/opencl/) implementations out there (and they're all working fine with JavaCL) :
  * [MacOS X 10.6 Snow Leopard](http://www.apple.com/macosx/technology/#opencl) comes with OpenCL pre-installed (won't work on earlier MacOS X versions)
  * [AMD Accelerated Parallel Processing (APP) SDK (formerly ATI Stream)](http://developer.amd.com/gpu/AMDAPPSDK/Pages/default.aspx) for ATI cards and/or any [SSE2](http://en.wikipedia.org/wiki/SSE2)-enabled CPUs on all flavours of Linux and Windows (even works on [Atom](http://en.wikipedia.org/wiki/Intel_Atom) CPUs !)
> _Note that ATI Stream's CPU implementation only supports images in version 2.4_ (before, it will just crash your app !)

> _Also note_ that you don't need to install Catalyst drivers or build anything from the APPSDK package : simply untar the package, set up your LD\_LIBRARY\_PATH and sudo-untar the ICD registration package.
  * [NVIDIA drivers](http://www.nvidia.com/object/cuda_opencl_new.html) for NVIDIA cards
  * [Intel OpenCL SDK](http://software.intel.com/en-us/articles/intel-opencl-sdk/) for Intel CPUs on Windows and Linux

## Optional : OpenGL bindings ##

If you want to share data between OpenCL and OpenGL, you'll need an OpenGL library such as JOGL or [LWJGL](http://lwjgl.org/).

### Lightweight Java Game Library (LWJGL) ###

The excellent [Lightweight Java Game Library (LWJGL)](http://lwjgl.org/) is a premier choice for OpenGL and OpenAL cross-platform programming in Java, providing a fast close-to-metal API.

It [can be installed in many various ways, including Maven](http://www.lwjgl.org/wiki/index.php?title=Main_Page#Getting_started).

Note that it also provides OpenCL bindings, but they are much closer to the metal and might be substantially harder to use than JavaCL for beginners and advanced users alike.

### Jogamp JOGL ###

[Jogamp JOGL](https://jogamp.org/jogl/www/) is probably the most feature-full OpenGL binding library.

# Use JavaCL in a project #

## With Maven ##

JavaCL and its plugin can be used with [Maven](http://maven.apache.org/), simply edit your `pom.xml` as follows :
```
<project>
  <dependencies>

    <dependency>
      <groupId>com.nativelibs4java</groupId>
      <artifactId>javacl</artifactId>
      <version>1.0.0-RC4</version>
    </dependency>

  </dependencies>

  <build>
    <plugins>

      <plugin>
        <groupId>com.nativelibs4java</groupId>
        <artifactId>maven-javacl-plugin</artifactId>
        <version>1.0.0-RC4</version>
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

If you wish to use a previous release (1.0.0-RC1, for instance) or a snapshot (1.0-SNAPSHOT), you'll need the following repositories :
```
<project>
  <repositories>
    <repository>
      <id>sonatype</id>
      <name>Sonatype OSS Snapshots Repository</name>
      <url>http://oss.sonatype.org/content/groups/public</url>
    </repository>
    <repository>
      <id>nativelibs4java</id>
      <name>nativelibs4java Maven2 Repository</name>
      <url>http://nativelibs4java.sourceforge.net/maven</url>
    </repository>
  </repositories>
  <pluginRepositories>
      <pluginRepository>
          <id>sonatype</id>
          <name>Sonatype OSS Snapshots Repository</name>
          <url>http://oss.sonatype.org/content/groups/public</url>
      </pluginRepository>
  </pluginRepositories>

</project>
```

Please read [this wiki page](JavaCLGenerator.md) for more information about JavaCL Generator.

## With sbt (simple-build-tool) ##

Add these two lines to your project file :
```
import sbt._
class MyProject(info: ProjectInfo) extends DefaultProject(info)
{
  // Repository for snapshots :
  // val sonatypeOSSRepo = "Sonatype OSS Repository" at "http://oss.sonatype.org/content/groups/public"
  val javacl = "com.nativelibs4java" % "javacl" % "1.0.0-RC4"
}
```

## With OSGi ##

JavaCL is compatible with OSGi _a minima_, which means it should now load fine in any OSGi container but you might find some OSGi features missing : [please report any issue](https://github.com/ochafik/nativelibs4java/issues/new) you find.

## Raw download ##

If you're not using Maven, Gradle, sbt or any other build manager, please proceed to [Downloads](Downloads.md) and add `javacl-x.x.x-shaded.jar` to your classpath manually :

Compile some code :
```
javac -cp javacl-x.x.x-shaded.jar MyTest.java
```

Run the code (replace the semi-colon ';' by ':' on Unix systems) :
```
java -cp javacl-x.x.x-shaded.jar;. MyTest
```

# Install / Build from sources #

Please read the [Build](Build.md) page.