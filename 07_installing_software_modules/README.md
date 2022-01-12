# Software

Enterprise Linux configurations like those used on MATILDA often have very
out of date system packages. This provides great stability, but is often not
useful for development and data science.

The way around this is the `module` system.

## Using Pre-Installed Software with Environment **Modules**

Several applications and software libraries are already available on Matilda, and can be accessed through [environment modules](https://kb.oakland.edu/uts/HPCSoftware).

### What Modules Are Available?

To see the modules that are available on a cluster, run this command:

```
$ module avail
```

To see the module avail for a specific software, for example Julia, then do:

```
$ module avail tensorflow

--------------------------------------------------------------- /cm/shared/modulefiles ---------------------------------------------------------------
   TensorFlow/2.4.1-py385-cuda1022        tensorflow-py37-cuda10.2-gcc/1.15.3
   TensorFlow/2.5.0-py390-cuda1111 (D)    tensorflow2-py37-cuda10.2-gcc/2.2.0

  Where:
   D:  Default Module

Module defaults are chosen based on Find First Rules due to Name/Version/Version modules found in the module tree.
See https://lmod.readthedocs.io/en/latest/060_locating.html for details.

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".
```
### How Do I Use a Module?

Use the `module load <module-name>` command to activate a module.

For example, to use TensorFlow:

```
$ module load TensorFlow/2.4.1-py385-cuda1022
```
Note that you need to include the full path, with the version you've selected, for it to work. For example, if using tensorflow, the command `module load TensorFlow/2.4.1-py385-cuda1022` specifies the full path, while the command `module load tensorflow` does not.

To see all of the options for the module command you can run `module help`

### How Do I Get Rid of a Module?

To unload a specific module, use `module unload <name>`. You can unload all modules by simply relogging into the cluster, or more easily, via the command `module purge`.

### How Do Modules Work?

Modules work by setting various environment variables, especially your `$PATH`, to point to the appropriate, more up-to-date installations provided as part of the software on the cluster.

To see exactly what a module is doing, use the command `module show <module-name>`.

For example:

```
$ module show TensorFlow/2.4.1-py385-cuda1022 
--------------------------------------------------------------------------------------------------------------------------------------------------
   /cm/shared/modulefiles/TensorFlow/2.4.1-py385-cuda1022.lua:
--------------------------------------------------------------------------------------------------------------------------------------------------
help([[
Description
===========
TensorFlow is an end-to-end open source platform for machine learning.

More information
================
 - Homepage: https://www.tensorflow.org/
]])
whatis("TensorFlow is an end-to-end open source platform for machine learning.")
whatis("Homepage: https://www.tensorflow.org/")
depends_on("Python/3.8.7")
depends_on("cudnn7.6-cuda10.2/7.6.5.32")
depends_on("TensorRT/7.0.0.11-cuda102-cudnn76")
depends_on("git-gcc/2.29.2")
prepend_path("PATH","/cm/shared/apps/TensorFlow/2.4.1-py385-cuda1022/bin")
prepend_path("PYTHONPATH","/cm/shared/apps/TensorFlow/2.4.1-py385-cuda1022/lib/python3.8/site-packages")
prepend_path("LD_LIBRARY_PATH","/cm/shared/apps/nvhpc/Linux_x86_64/21.1/cuda/11.0/targets/x86_64-linux/lib")
```
Again, modules only change the values of environment variables. They do not install or uninstall software.

### A Word on Python Modules
<!--
When you first connect to the cluster, none of the module are loaded, and the system Python–which is old–will be used by default:

```
$ python --version
Python 3.6.8

$ which python
/usr/bin/python
```
-->

To get an Miniconda Python implementation (the recommended way to use Python), simply load one of the available miniconda3/ modules. For example:

```
$ module load miniconda3
$ python --version
Python 3.8.5
$ which python
/cm/shared/apps/miniconda3/4.9.2-py385/bin/python```
```
To see all the packages that are included in Anaconda Python run this command:

```
$ conda list
```

### Final Tips on Modules

Remember that no modules are loaded upon connecting to a cluster. Best practice is to load them each time.

**Don't put module commands in your .bashrc file.** You may be tempted to put these in `.bashrc`. Don't. By all means set up an alias for your use, but `.bashrc` is not implicitly loaded for a SLURM job. You're likely to set up a situation where you have tangled modules and not quite sure why your job is failing to behave as expected.

If you need software that is not installed or made available through **modules**, you will most likely have to install the software yourself. The following section provides the needed guidelines.

## Installing Software Not Available on the Clusters

In general, to install software not available as a module, we recommend that you create a directory such as `/home/<YourNetID>/software` to build and store software. 

One exception to this general recommendation is when installing Python packages. Python packages are installed by default in your home directory, and therefore don't require that you set up a special folder for them.

Two notes:

+ Make sure you have enough space. Errors found when installing packages can often come down to this.
+ Commands like `sudo yum install` or `sudo apt-get install` will not work.


### Using Software Containers

Software containers can be really useful when you need software that may have tricky dependencies. You can pull and run an image (essentially a large file) that contains the software and everything it needs. 

We do not allow [Docker](https://www.docker.com) but [Singularity](https://researchcomputing.princeton.edu/support/knowledge-base/singularity) can be used. You can still [search for and use images from Docker](https://hub.docker.com/), you just need to use Singularity commands. For example:

```
$ singularity pull docker://hello-world
$ singularity run hello-world_latest.sif
...
Hello from Docker!
This message shows that your installation appears to be working correctly.
...
```

### Compiling Software, GNU Compiler Collection (GCC)

Software that comes in source form must be compiled before it can be installed in your `/home` directory.

One popular tool suite for doing this is the GNU Compiler Collection (GCC) which is composed of compilers, a linker, libraries, and tools.


```
g++ --version
g++ (GCC) 8.3.1 20191121 (Red Hat 8.3.1-5)
Copyright (C) 2018 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

When compiling a parallel code that uses the message-passing interface (MPI), you will need to load an MPI module. You can load the Intel compilers and Intel MPI library with:

```
$ module load intel/19.1.1.217 intel-mpi/intel/2019.7 
Loading intel/19.1.1.217
  Loading requirement: intel-mkl/2020.1

Loading intel-mpi/intel/2019.7
  Loading requirement: ucx/1.9.0
$ mpicc --version
icc (ICC) 19.1.1.217 20200306
```

Note that the C and Fortran compilers and related tools are also updated by this method which is important for some software. The relevant tools are `gcc`, `g++`, `gfortran`, `make`, `ld`, `ar`, `as`, `gdb`, `gprof`, `gcov` and more.



### Vectorization

Modern CPUs can perform more than one operation per cycle using vector execution units. A common example is elementwise vector addition.

[Vectorized code](https://en.wikipedia.org/wiki/Automatic_vectorization) generated for one processor will not run on another processor unless it supports those instructions. Such an attempt will produce an `illegal instruction` error if the instructions are not supported.
