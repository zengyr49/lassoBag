# SYSUCC-RNAseqPipe installation 

> adjusted from nf-core   

- [Installation](#installation)
  * [1) Install NextFlow](#1--install-nextFlow)
  * [2) Install the pipeline](#2--install-the-pipeline)
  * [3) Pipeline configuration](#3--pipeline-configuration)
      - [3.1) Software deps: Docker](#31--software-deps--docker)
      - [3.2) Software deps: Singularity](#32--software-deps--singularity)
      - [3.3) Software deps: conda](#33--software-deps--conda) 
  * [4) Install Software Manually](#4--install-software-manually)


## 1) Install NextFlow
Nextflow runs on most POSIX systems (Linux, Mac OSX etc). It can be installed by running the following commands:

```bash
# Make sure that Java v8+ is installed:
java -version

# Install Nextflow
curl -fsSL get.nextflow.io | bash

# Add Nextflow binary to your PATH:
mv nextflow ~/bin/
# OR system-wide installation:
# sudo mv nextflow /usr/local/bin
```

See [nextflow.io](https://www.nextflow.io/) for further instructions on how to install and configure Nextflow.

## 2) Install the pipeline  


#### 2.1) Development  

The whole pipeline is hosted in github. To install, you'll need to download and transfer the pipeline files manually:

```bash
mkdir -p ~/my-pipelines/
git clone https://github.com/likelet/RNAseqPipe.git 
cd ~/my_data/
nextflow run ~/my-pipelines/circPipe
```

To stop nextflow from looking for updates online, you can tell it to run in offline mode by specifying the following environment variable in your ~/.bashrc file:

```bash

export NXF_OFFLINE='TRUE'
```

#### 2.2) Development

If you would like to make changes to the pipeline, it's best to make a fork on GitHub and then clone the files. Once cloned you can run the pipeline directly as above.


## 3) Pipeline configuration
By default, the pipeline runs with the `standard` configuration profile. This uses a number of sensible defaults for process requirements and is suitable for running on a simple (if powerful!) basic server. You can see this configuration in [`conf/base.config`](../conf/base.config).

Be warned of two important points about this default configuration:

1. The default profile uses the `local` executor
    * All jobs are run in the login session. If you're using a simple server, this may be fine. If you're using a compute cluster, this is bad as all jobs will run on the head node.
    * See the [nextflow docs](https://www.nextflow.io/docs/latest/executor.html) for information about running with other hardware backends. Most job scheduler systems are natively supported.
2. Nextflow will expect all software to be installed and available on the `PATH`

#### 3.1) Software deps: Docker
First, install docker on your system: [Docker Installation Instructions](https://docs.docker.com/engine/installation/)


Then, running the pipeline with the option `-profile standard,docker` tells Nextflow to enable Docker for this run. An image containing all of the software requirements will be automatically fetched and used from dockerhub (https://hub.docker.com/r/likelet/RNAseqPipe/).

#### 3.2) Software deps: Singularity
If you're not able to use Docker then [Singularity](http://singularity.lbl.gov/) is a great alternative.
The process is very similar: running the pipeline with the option `-profile standard,singularity` tells Nextflow to enable singularity for this run. An image containing all of the software requirements will be automatically fetched and used from singularity hub.

If running offline with Singularity, you'll need to download and transfer the Singularity image first:

```bash
singularity pull --name RNAseqPipe.simg shub://likelet/RNAseqPipe
```

Once transferred, use `-with-singularity` and specify the path to the image file:

```bash
nextflow run /path/to/circPipe -with-singularity circPipe.simg
```

Remember to pull updated versions of the singularity image if you update the pipeline.

#### 3.3) Software deps: conda
If you're not able to use Docker _or_ Singularity, you can instead use conda to manage the software requirements.
This is slower and less reproducible than the above, but is still better than having to install all requirements yourself!
The pipeline ships with a conda environment file and nextflow has built-in support for this.
To use it first ensure that you have conda installed (we recommend [miniconda](https://conda.io/miniconda.html)), then follow the same pattern as above and use the flag `-profile standard,conda`