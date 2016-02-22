

## `OpenWorm` ##

[OpenWorm](http://www.openworm.org/) is an ambitious life-simulation project that tackles "the greatest puzzle of our time - what are all the events that happen inside a living brain?" (to achieve that long-term goal, it starts with a not-so-simple worm simulation that deals with neural networks, chemistry and physics simulation...)

It [uses JavaCL](http://www.openworm.org/index.html#/tech) to perform GPU-powered simulations.

## `Encog` ##

"[Encog](http://www.heatonresearch.com/encog) is an advanced neural network and machine learning framework."

There appears to be [experimentations with JavaCL](http://www.heatonresearch.com/wiki/JavaCL) (and other bindings) to perform GPGPU.

## `STEM` ##

The [Spatiotemporal Epidemiological Modeler (STEM)](http://www.eclipse.org/stem/) tool is designed to help scientists and public health officials create and use spatial and temporal models of emerging infectious diseases. These models can aid in understanding and potentially preventing the spread of such diseases.

== `CellProfiler`

[CellProfiler](http://cellprofiler.org/) is free open-source software designed to enable biologists without training in computer vision or programming to quantitatively measure phenotypes from thousands of images automatically.

Its OpenCL backend uses JavaCL.

## `Jama` ##

[Jama](http://math.nist.gov/javanumerics/jama/) is a public-domain Java Matrix Library.
[mukis.de](http://muuki88.github.com/jama-osgi/project-summary.html) maintains a port that uses JavaCL for GPU-accelerated operations, available in Maven Central.

## `Calx (Clojure)` ##

[Calx](https://github.com/ztellman/calx) is an idiomatic Clojure wrapper for OpenCL.

It uses JavaCL underneath.

## `JavaCL Parallel Primitives` ##

The [JavaCL Parallel Primitives](http://gitorious.org/javaclpp/pages/Home) (from [Kazó Csaba](http://kazocsaba.blogspot.com/), who also [contributes to JavaCL](http://code.google.com/p/bridj/wiki/CreditsAndLicense)) provides reduction and scan utilities that go beyond [those provided in JavaCL](Goodies.md), and more...

## `ScalaCL` ##

[ScalaCL](http://code.google.com/p/scalacl/) (BSD-licensed) is an optimizing Scala compiler plugin + GPU-backed collections.

It uses JavaCL, which is still accessible directly if needed.

## Academia ##

### Papers and Theses ###

  * [Contributions to parallel stochastic simulation: Application of good software engineering practices to the distribution of pseudorandom streams in hybrid Monte-Carlo simulations](http://tel.archives-ouvertes.fr/docs/00/85/87/35/PDF/phd_thesis_jonathan_passerat-palmbach--reviewers_version.pdf), PhD Thesis by Jonathan Passerat-Palmbach, analyses in good detail JavaCL and ScalaCL.
  * [Shin Yoo](http://www.cs.ucl.ac.uk/staff/s.yoo/) used JavaCL in his paper [Highly Scalable Multi Objective Test Suite Minimisation Using Graphics Cards](http://www.ssbse.org/2011/presentations/Session%207/ssbse11_shin_yoo.pdf) (won the [best paper award at SSBSE 2011](http://www.ssbse.org/2011/prizes.html)).
  * [The use of GPU with OpenCL for simulation of a flocking model](http://www.csc.kth.se/utbildning/kandidatexjobb/datateknik/2011/rapport/karlsson_john_OCH_lofquist_lars_K11057.pdf) (in Swedish, with an English abstract) by John Karlsson, Lars Löfquist
  * [The Design and Implementation of a Bytecode for Optimization on Heterogeneous Systems](http://digitalcommons.trinity.edu/cgi/viewcontent.cgi?article=1027&context=compsci_honors), by Kerry A. Seitz

### Labs ###

  * The [JStar Research Group](http://www.cs.waikato.ac.nz/research/jstar/) from [The University of Waikato](http://www.waikato.ac.nz/)
  * The [Faculty of Sciences / Department of Computer Sciences](http://wiki.cs.vu.nl/mp/index.php/OpenCL_Java_high-level_API) from [The University of Amsterdam](http://www.cs.vu.nl/en/index.asp)