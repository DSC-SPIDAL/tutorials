---
title: "Pathology Image Data"
description: "Example usecase of DAMDS with Pathology Image Data"
tags: []
lastmod: 2017-02-10
date: "2017-02-10"
categories:
  - "Development"
  - "Example"
slug: "pathology_image_data_example"
---  

# Introduction

The data consist of several pathology images that are described using 96 features. There are 11 images in the dataset totaling upto around 4 million data points. The example will only use a very small
subset of the complete data set. The visualization listed below is a result of a larger run which contained ~220K data points in total. 220K data points where selected by 
20K random row samples extracted from each image. The plot is clustered by image. Each of the 11 clusters correspond to a single image, With distance matrix outliers set to 3*sigma

Visualization using WebPlotViz - https://spidal-gw.dsc.soic.indiana.edu/public/resultsets/1678860580

# Data

The filename encodes some information about the tile and the source image. For example, take the following file. 

“TCGA-86-6562-01Z-00-DX1.5dea3015-e606-4837-9f99-ac14f0aa091b_mpp_0.252_x81920_y49152-features.csv”. This file has results generated from image “TCGA-86-6562-01Z-00-DX1.5dea3015-e606-4837-9f99-ac14f0aa091b. Each pixel in the image is 0.252 microns (the _mpp value) in x and y directions. The image tile starts at x=81920 and y=49152 (the _x and _y values). Location (0,0) is the upper left corner of the image.

The files that start with the same image id collectively store all the results for that image. There are 11 images in this dataset


1. TCGA-55-6543-01Z-00-DX1.08806fe0-84d3-4fd6-8746-6cf557241958
2. TCGA-55-6969-01Z-00-DX1.713df9f6-1a91-4e0e-ad43-42e66dcca191
3. TCGA-55-6972-01Z-00-DX1.0b441ad0-c30f-4f63-849a-36c98d6e2d3b
4. TCGA-55-7914-01Z-00-DX1.875bffe1-8c56-4c29-ab11-6840ee3a643c
5. TCGA-55-7994-01Z-00-DX1.a0858080-c471-4337-bc57-7af57e9d92d8
6. TCGA-55-7995-01Z-00-DX1.fef24d04-35a0-4f57-8f51-7ad602a78871
7. TCGA-55-8087-01Z-00-DX1.548f2800-8caf-4c0e-a7b5-6d3d28315d9c
8. TCGA-55-8092-01Z-00-DX1.04e44341-c4bb-4b0b-965c-7ec83f3877a6
9. TCGA-55-8094-01Z-00-DX1.8dc29615-e124-4f17-81a1-c0b20c38d12c
10. TCGA-83-5908-01Z-00-DX1.381c8f82-61a0-4e9d-982d-1ad0af7bead9
11. TCGA-86-6562-01Z-00-DX1.5dea3015-e606-4837-9f99-ac14f0aa091b


Each row of a csv file (other than the first row, which is the header row) stores features computed for a segmented nucleus. There are 97 columns in each file. The first 96 columns are the features. The last column is the polygon representing the boundary of each segmented nucleus in the corresponding image tile. The coordinates of the polygon points are global with respect to the image.

`Note: For this tutorial we will only consider a single image "TCGA-55-8094-01Z-00-DX1.8dc29615-e124-4f17-81a1-c0b20c38d12c" for simplicity.`

Sample data set can be found here - https://github.com/DSC-SPIDAL/applications/tree/master/pathalogy-image-data/sampledata 

# Prerequisites
 
 In order to perform the pre processing download the jar file from [here](https://github.com/DSC-SPIDAL/applications/tree/master/pathalogy-image-data/bin) (use the jar with dependencies) or clone and build from the source. The source code for the programs are available [here](https://github.com/DSC-SPIDAL/applications/tree/master/pathalogy-image-data) and can be built using the `mvn clean install` command

{{< highlight javascript >}}
 //Commands and steps
 //If you have not the applications git repo perform the following
 git clone https://github.com/DSC-SPIDAL/applications.git
 cd applications/pathalogy-image-data
 mvn clean install
 cd sampledata
 unzip data.zip
{{< /highlight >}}

After building it will create a Jar file inside the `target` directory.

# Pre Processing

1. Step 1: 
 Single data files need to be created for each image or a single data file needs to be created from all the images. Number of data points in each image varied. The total number of data poitns (rows in files) was around 3.95 million. The program SingleFileGeneration allows the generation of two types of tiles. First it can simply gather all the rows corresponding to a single image to a single file. Secondly it can extract portions of rows from each image to create a collated data file.
 Note: All the required script files can be found in the [scripts](https://github.com/DSC-SPIDAL/applications/tree/master/pathalogy-image-data/scripts) folder in the git repo.
 In 
 `run_single_file_gen.sh` will run a java that creates a single file from the set of image files. The program takes the several inputs and can be configured by editing the `run_single_file_gen.sh` using an text editor.
 
 * `cp=<path-to-git>/applications/pathalogy-image-data/target/pathology_image_dat-jar-with-dependencies.jar` -- Needs to point to the jar that was downloaded or built "pathology_image_dat-jar-with-dependencies.jar"
 * `dataFolder=<path-to-git>/applications/pathalogy-image-data/sampledata/data` -- Folder that poitns to the sample data ex - /home/tutorial/data
 * `FileName=TCGA-55-8094-01Z-00-DX1.8dc29615-e124-4f17-81a1-c0b20c38d12c` -- The name of the image to be filtered out, no need to change this. 
 * `OuputFolder=<path-to-git>/applications/pathalogy-image-data/sampledata` -- Folder to write the output file ex - /home/tutorial/results
 * `OutputFileName=singleImage`-- Name of the output file
 * `numPoints=2000` -- Number of data points to collect, keep this to a small number to keep runtime small ex- 2000
 
 {{< highlight javascript >}}
  //Commands and steps
  cd applications/pathalogy-image-data/scripts/preprocessing/
  vim run_single_file_gen.sh // Make changes mentioned above
  ./run_single_file_gen.sh
 {{< /highlight >}}
 
 This will create a file named `singleimage.data` in the `<path-to-git>/applications/pathalogy-image-data/sampledata` folder.
 
2. Step 2
 Distance calculation is done using the data files generated in step 1. DistanceCalculation does this operation in parallel using MPI. `run_distance_calculation.sh` script file will run the Distance Calculation program to generate the distance matrix for the file 
 generated in step 1. The program takes the following parameters which can be modified by opening the `run_distance_calculation.sh` file in an text editor.
 
 * `cp=$cp":<path-to-git>/applications/pathalogy-image-data/target/pathology_image_dat-jar-with-dependencies.jar"` -- Need to add the jar file to the classpath
 * `dataFile=<path-to-git>/applications/pathalogy-image-data/sampledata/singleimage.data` -- Data file that was generated in step 1. 
 * `points=2000` -- number of points in data file
 * `dimension=96` -- number of dimensions in the data file
 * `outFile=<path-to-git>/applications/pathalogy-image-data/sampledata/out.bin` -- output file name
 
  {{< highlight javascript >}}
   //Commands and steps
   cd applications/pathalogy-image-data/scripts/preprocessing/
   vim run_distance_calculation.sh // Make changes mentioned above
   ./run_distance_calculation.sh
  {{< /highlight >}}
  
   This will create a file named `out.bin` in the `<path-to-git>/applications/pathalogy-image-data/sampledata` folder.

 
# Dimension reduction with MDS
 The resulting data files obtained by step 2 are used to perform dimension reduction using DAMDS. `run_mds.sh` file can be used to run the DAMDS algorithm on the data set that was generated. Only one parameter needs to specified in the file.
 
 * `confFile=/home/pulasthi/work/FushengWang/tutorial/config.properties` -- Points to the configuration file that is used by DAMDS
 
 An sample conf file can be found in the scripts folder. The configurations specifies many parameters, but for the purpose of the tutorial only two of them need to adjusted.
 
 * `DistanceMatrixFile = /home/tutorial/results/out.bin` -- Needs to point to the distance file generated in step 2
 * `NumberDataPoints = 2000` -- specifies the number of data points in the file. The example values used 2000 when creating the single data file so the value should reflect that.
 
 The DAMDS program will generate several output files. The file that contains the data points that were reduced to 3 dimensions is "damds-points.txt" ( This can be configured in the properties file ).
 You can open the file in an text editor to look at the generated data points. 
 
 {{< highlight javascript >}}
    //Commands and steps
    cd applications/pathalogy-image-data/scripts/mds/
    vim config.properties // Make changes mentioned above
    ./run_mds.sh
   {{< /highlight >}}
 
 This will create a file named `damds-points.txt` in the `<path-to-git>/applications/pathalogy-image-data/scripts/mds` folder.

 
 
# Visualization
 The 3D data generated from MDS can be visualized using WebPlotViz. In order upload the points file to the tool first it needs to be formatted to the correct format. `format_data.sh` performs the necessary formatting. The script takes the file that needs
 to be formatted and creates a formatted file with same name and ".formatted" at the end. Ex "./format_data.sh damds-points.txt" will output "damds-points.txt.formatted". This file can then be uploaded to
 WebPlotViz to be visualized.
 
 {{< highlight javascript >}}
     //Commands and steps
      cd applications/pathalogy-image-data/scripts
      ./format_data.sh mds/damds-points.txt
 {{< /highlight >}}
 
  This will create a file named `damds-points.txt.formatted` in the `<path-to-git>/applications/pathalogy-image-data/scripts/mds` folder. Upload this file to [WebPlotViz](https://spidal-gw.dsc.soic.indiana.edu/) to visualize the results in 3D space

 
 The resulting data is visualised in WebPlotViz https://spidal-gw.dsc.soic.indiana.edu/public/resultsets/1797346761. Note that since this example was done only with 2000 data points there is no visible structure.
 
 

