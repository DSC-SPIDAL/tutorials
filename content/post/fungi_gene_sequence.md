---
title: "Fungi Gene Sequence Clustering"
description: "Example usecase of DAMDS and DAPWC with gene sequence data for clustering"
tags: []
lastmod: 2017-02-10
date: "2017-02-10"
categories:
  - "Development"
  - "Example"
slug: "fungi_gene_sequence_example"
---


# Introduction

 The usecase of this example is based on a set fungal gene sequence data. For the pupose of the example we will only use a very small subset of the complete data to
 keep the runtime at a manageable level. The aim is to cluster the gene sequence data. For this DAMDS and DAPWC algorithms will be utilized. When working with the complete data set the 
 following steps are followed ( Some steps are done iteratively to refine results further ) to generate good clustering results. For this example will only be doing a single iteration.
 
 
 * 1: Process raw data to find Unique sequences that appear more than once ( 170K resulting sequences ) 
 * 2: Run Smith–Waterman algorithm to generate distance matrix
 * 3: Run MDS to genereate 3D data points for the data using the distance matrix
 * 4: Run DAPWC with a low number of target clusters ( 8 clusters were used in this case ). 
 * 5: Visualize the resulting 8 clusters to estimate number of sub clusters in each one. If 2 or more of the 8 clusters seem to be more proper when merged, they can be merged before the next iteration
 * 6: Run DAPWC for each of the 8 clusters separately, specifying the appropriate number of clusters for each one. 
 * 7: Visualize the new clusters and merge them were needed
 * 8: Collect all the clusters into a single file for the final cluster result.
 
 Results: Below are visualizations of the two major steps:
 
 
 * Results for the first DAPWC run showing the first 8 clusters - https://spidal-gw.dsc.soic.indiana.edu/public/resultsets/1828982764 
 * Final result with 65 clusters - https://spidal-gw.dsc.soic.indiana.edu/public/resultsets/1273112137
 
 Note: For some intermediate steps data needs to be formatted before the next step can be completed. To keep the example simple the repository will provide formatted data for such cases

# Data

The raw data used for the complete use case is in FASTA format. Some analysis data on the complete data set is listed below.

   * There was a total of 7065846 (~7million) sequences.
   * Number of Unique sequences : 578486
   * Number of Unique sequences that appear more than once  : 170264
   * Number of sequence of Single Occurrence  : 408222
   
For the complete use case all 170K unique sequences that appear more than once were used. For the example we will only be using 2400 gene sequences.

# Pre Processing

The file `2400_gene_seq_testset.fna` contains the 2400 gene sequences that are used in this example. This file can be found at the git repo [here](https://github.com/DSC-SPIDAL/applications/tree/master/fungi-gene-sequence/data) . In order to run DAMDS and DAPWC we need to
generate the corresponding distance matrix for the data file. For this usecase distance between two gene sequences are calculated using Smith–Waterman algorithm. The code for the parallel implmentaion of [Smith–Waterman](https://github.com/DSC-SPIDAL/csharp/tree/master/SalsaTPL/Salsa.SmithWatermanMS) algorithm is available 
[here](https://github.com/DSC-SPIDAL/csharp/tree/master/SalsaTPL/Salsa.SmithWatermanMS). This is very time consuming calculation even for the small dataset hence the git repo provides the distance matrix that is generated for the sample dataset.
The distance matrix `distance-matrix.bin` which is avilable in the git repo [data folder](https://github.com/DSC-SPIDAL/applications/tree/master/fungi-gene-sequence/data).

Commands and steps
If you have not the applications git repo perform the following
{{< highlight javascript >}}
 //Commands and steps
 //If you have not the applications git repo perform the following
 git clone https://github.com/DSC-SPIDAL/applications.git
{{< /highlight >}}

# Dimension reduction with MDS

In order to visualize the data we will perform dimension reduction to reduce the data to 3D using DAMDS algorithm. The data points generated from the DAMDS algirhtm can be later used with the clusters generated
with DAPWC to visualize the data in WebPlotViz. 

To run mds locally [`run_mds.sh`](https://github.com/DSC-SPIDAL/applications/tree/master/fungi-gene-sequence/scripts/damds) script file can be used. To run in an HPC envirnment the script files inside the [hpcscripts](https://github.com/DSC-SPIDAL/applications/tree/master/fungi-gene-sequence/scripts/damds/hpcscripts) folder can bs used.
In order to run the program you must configure the config.properties file to point to the `distance-matrix.bin` file, and edit the `run_mds.sh` file to point to the `config.properties` file. Once the configurations are
done you can simple run the program by executing `./run_mds.sh` from the shell.

The run will create a points file name `damds-points.txt` which will contain the 3D data points. In order to upload this data file to WebPlotViz you need to format the data to the correct format. This can be
done using the `format_data.sh` script. The script takes a file name as an argument and formats that file and outputs the formatted file with .foramatted extention. ex usage `./format_data.sh damds-points.txt`.

{{< highlight javascript >}}
//Commands and steps - excute 1 command at a time
 cd applications/fungi-gene-sequence/scripts/damds/
 vim config.properties // Perform needed config changes, Only need to correct the path for the distance file
 vim run_mds.sh // check that the correct config.properties file is configured
 ./run_mds.sh
 cd ..
 ./format_data.sh damds/damds-points.txt // will create the formatted data file in the damds folder
{{< /highlight >}}

The resulting data is visualised in WebPlotViz - https://spidal-gw.dsc.soic.indiana.edu/public/resultsets/1002319877

# Clustering with DAPWC

The main objective of this usecase is to cluster the data. For this we will use the DAPWC algorithm. DAPWC can work directly with the distance matrix to create clusters. The program will output a set of clusters
and the data points that are assigned to each cluster. We can configure the number of clusters that we expect from the algorithm. A rough amount of clusters can be determined from the visualization we did in the
previous step. 

In order to run DAPWC first we need to configure the `config.properties` file that is in [scripts/dapwc](https://github.com/DSC-SPIDAL/applications/tree/master/fungi-gene-sequence/scripts/dapwc) folder. The following parameters need to be set properly.

ClusterFile -- the output file which will contain the resulting clusters. Note: The output file changes slightly for example if you give "cluster.txt" and give 10 for MaxNcent the output file name will be "cluster-M10-C10txt"
DistanceMatrixFile -- `distance-matrix.bin` file which contains the distance matrix
MaxNcent -- the number of clusters to look for
NumberDataPoints -- the number of data points in the distance matrix, this is 2400 for this example

Then we need to configure the `run_dapwc.sh` file to point to the `config.properties` file ( If running in an HPC environment use the files in [hpcscripts](https://github.com/DSC-SPIDAL/applications/tree/master/fungi-gene-sequence/scripts/dapwc/hpcscripts) ).

Once the configuration is complete the program can be run using the command `./run_dapwc.sh`. 

{{< highlight javascript >}}
//Commands and steps - excute 1 command at a time
 cd applications/fungi-gene-sequence/scripts/dapwc/
 vim config.properties // Perform needed config changes,Only need to correct the path for the distance file
 vim run_mds.sh // check that the correct config.properties file is configured
 ./run_dapwc.sh
{{< /highlight >}}


# Merging results and visualizing

Once we have outputs from both DAPWC and DAMDS algorithms we can merge the results and visualize them on WebPlotViz. In order the merge the data the script `merge_and_format.sh` can be used. The script takes in 
two arguments and will generate a file that can be uploaded to WebPlotViz

1. The output file from the DAMDS run (ex damds-points.txt)
2. The output file from the DAPWC run (ex cluster-M10-C10txt)

{{< highlight javascript >}}
//Commands and steps - excute 1 command at a time
 cd applications/fungi-gene-sequence/scripts/
 ./merge_and_format.sh damds/damds-points.txt dapwc/cluster-M10-C10txt
{{< /highlight >}}

The output file will be `points.formatted.txt`. Upload this file to [WebPlotViz](https://spidal-gw.dsc.soic.indiana.edu/) to visualize the results in 3D space

The resulting data is visualised in WebPlotViz - https://spidal-gw.dsc.soic.indiana.edu/public/resultsets/280333944





