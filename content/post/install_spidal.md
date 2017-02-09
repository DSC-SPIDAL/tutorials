---
title: "Installing SPIDAL Software"
description: "Install MDS and DAVS"
tags: []
lastmod: 2015-12-23
date: "2012-04-06"
categories:
  - "Development"
  - "VIM"
slug: "install_spidal"
---

DA-MDS
======
 
The project can be built from the source.
 
{{< highlight javascript >}}
 git clone https://github.com/DSC-SPIDAL/damds.git
 cd damds
 mvn install
{{< /highlight >}}

After building it will create a Jar file inside the `target` directory.

Usually people run MDS using a cluster manager such as slurm. We have provided a sample script in `bin` directory that can be used to run the program on a single machine.

Run example
-----------

DA-MDS is configured using a configuration file. A sample configuration file with two input files for distance and weight matrices can be found in `examples/input` folder.

Lets run this sample using the `damds.sh` file found in `bin` directory.
 
{{< highlight javascript >}}
 cd bin
 ./damds.sh ../examples/input/config.properties 1 4 
{{< /highlight >}}

The first argument to the script is the location of configuration file. Second argument is the number of nodes. 
In our case it is 1 as we run on local machine. The third argument is the number of processes. In this case we are using 4 processes.

After the program finished executing, the output will be in a file called `damds-points.txt` inside the `bin` directory.

You can upload this file to `WebPlotViz` using the following link to visualize the data. 

{{< highlight javascript >}}
https://spidal-gw.dsc.soic.indiana.edu/dashboard
{{< /highlight >}}




