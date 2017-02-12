---
title: "Installing SPIDAL Software"
description: "Install MDS and DAVS"
tags: []
lastmod: 2017-02-10
date: "2017-02-10"
categories:
  - "Development"
  - "VIM"
slug: "install_spidal"
---

common
======

SPIDAL libraries depend on the common project in DSC-SPIDAL github. We need to build it first.

{{< highlight javascript >}}
 git clone https://github.com/DSC-SPIDAL/common.git
 cd common
 mvn install
{{< /highlight >}}

DA-MDS
======

DA-MDS is the deterministic annealing implementation of Multidimensional Scaling algorithm. The project can be built from the source.
 
{{< highlight javascript >}}
 git clone https://github.com/DSC-SPIDAL/damds.git
 cd damds
 mvn install
{{< /highlight >}}

After building it will create a Jar file inside the `target` directory.

Usually people run MDS using a cluster manager such as slurm. We have provided a sample script in `bin` directory that can be used to run the program on a single machine.

Run example - local machine
---------------------------

DA-MDS is configured using a configuration file. A sample configuration file with two input files for distance and weight matrices can be found in `examples/input` folder.

Lets run this sample using the `damds.sh` file found in `bin` directory.
 
{{< highlight javascript >}}
 cd bin
 ./damds.sh ../examples/input/config.properties 1 4 
{{< /highlight >}}

The first argument to the script is the location of configuration file. Second argument is the number of nodes. 
In our case it is 1 as we run on local machine. The third argument is the number of processes. In this case we are using 4 processes.

After the program finished executing, the output will be in a file called `damds-points.txt` inside the `bin` directory.

You can upload this file to `WebPlotViz` using the following link to visualize the data. Note: you may need to register in WebPlotViz before you can upload the files. 

{{< highlight javascript >}}
https://spidal-gw.dsc.soic.indiana.edu/dashboard
{{< /highlight >}}


Run example - slurm cluster
---------------------------

You can use the `damds_slurm.sh` found in `bin` directory to run the program in a `slurm` HPC cluster. In this example we assume that you have built `damds` in a shared file system where every node can access the data files with the same file path.

{{< highlight javascript >}}
 cd bin
 ./damds_slurm.sh ../examples/input/config.properties 4 
{{< /highlight >}}

The above command will run the example using 4 nodes. 


DAPWC
=====

Deterministic Annealing Pairwise Clustering (dapwc) is a scalable and parallel clustering program that operate on non vector space
 
{{< highlight javascript >}}
 git clone https://github.com/DSC-SPIDAL/dapwc.git
 cd dapwc
 mvn install
{{< /highlight >}}

After building it will create a Jar file inside the `target` directory.