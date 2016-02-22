See [this thread on NativeLibs4Java's mailing list](http://groups.google.com/group/nativelibs4java/browse_thread/thread/d4b93879ceaaf6e9/e56ecb566bfcbb6a)

Long story short : before running your JavaCL application, set the LD\_PRELOAD environment variable as follows :
```
export LD_PRELOAD=/usr/lib/jvm/java-6-openjdk/jre/lib/amd64/libjsig.so 
```