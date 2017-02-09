---
title: "Installing OpenMPI"
description: "Install OpenMPI 1.10.1 on a Linux machine"
tags: [ "OpenMPI", "Install"]
lastmod: 2015-12-23
date: "2012-04-06"
categories:
  - "Development"
  - "VIM"
slug: "install_ompi.md"
---

# Prerequisites

1. Operating System
  * SPDIAL is extensively tested and known to work on,
    *  Red Hat Enterprise Linux Server release 6.7 (Santiago)
    *  Red Hat Enterprise Linux Server release 5.10 (Tikanga)
    *  Ubuntu 14.04 LTS 
  * This may work in Windows systems depending on the ability to setup OpenMPI properly, however, this has not been tested and we recommend choosing a Linux based operating system instead.

2. Java
  * Download Oracle JDK 8 from http://www.oracle.com/technetwork/java/javase/downloads/index.html
  * Extract the archive to a folder named jdk1.8.0
  * Set the following environment variables.
```bash
    JAVA_HOME=<path-to-jdk1.8.0-directory>
    PATH=$JAVA_HOME/bin:$PATH
    export JAVA_HOME PATH
```
3. Apache Maven
  * Download latest Maven release from http://maven.apache.org/download.cgi
  * Extract it to some folder and set the following environment variables.
```java
    MVN_HOME=<path-to-Maven-folder>
    $PATH=$MVN_HOME/bin:$PATH
    export MVN_HOME PATH
```

5. OpenMPI
  * We recommend using `OpenMPI 1.10.1` although it work with the previous 1.8 versions. Note, if using a version other than 1.10.1 please remember to set Maven dependency appropriately in the `pom.xml`.
  * Download OpenMPI 1.10.1 from http://www.open-mpi.org/software/ompi/v1.10/downloads/openmpi-1.10.1.tar.gz
  * Extract the archive to a folder named `openmpi-1.10.1`
  * Also create a directory named `build` in some location. We will use this to install OpenMPI
  * Set the following environment variables
```bash
    BUILD=<path-to-build-directory>
    OMPI_1101=<path-to-openmpi-1.10.1-directory>
    PATH=$BUILD/bin:$PATH
    LD_LIBRARY_PATH=$BUILD/lib:$LD_LIBRARY_PATH
    export BUILD OMPI_1101 PATH LD_LIBRARY_PATH
```
  * The instructions to build OpenMPI depend on the platform. Therefore, we highly recommend looking into the `$OMPI_1101/INSTALL` file. Platform specific build files are available in `$OMPI_1101/contrib/platform` directory.
  * In general, please specify `--prefix=$BUILD` and `--enable-mpi-java` as arguments to `configure` script. If Infiniband is available (highly recommended) specify `--with-verbs=<path-to-verbs-installation>`. Usually, the path to verbs installation is `/usr`. In summary, the following commands will build OpenMPI for a Linux system.
```
    cd $OMPI_1101
    ./configure --prefix=$BUILD --enable-mpi-java
    make;make install
```
  * If everything goes well `mpirun --version` will show `mpirun (Open MPI) 1.10.1`. Execute the following command to instal `$OMPI_1101/ompi/mpi/java/java/mpi.jar` as a Maven artifact.
```
    mvn install:install-file -DcreateChecksum=true -Dpackaging=jar -Dfile=$OMPI_1101/ompi/mpi/java/java/mpi.jar -DgroupId=ompi -DartifactId=ompijavabinding -Dversion=1.10.1
```
  * Few examples are available in `$OMPI_1101/examples`. Please use `mpijavac` with other parameters similar to `javac` command to compile OpenMPI Java programs. Once compiled `mpirun [options] java -cp <classpath> class-name arguments` command with proper values set as arguments will run the MPI Java program.