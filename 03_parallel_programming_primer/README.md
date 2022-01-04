# Parallel Programming Primer

A common misconception is that simply running your code on a cluster will result in your code running faster. **Clusters do not run code faster by magic - for improved performance the code must be modified to run in parallel and that is explicitly done by the programmer.** In other words, the burden of modifying code to take advantage of multiple cores or nodes is on **you** as the programmer.

Although learning *how* to parallelize code is outside the scope of this workshop, for our purposes it is useful to at least be familiar with the typical ways in which code can be parallelized.

This guide will provide a very basic introduction to parallel programming concepts, with some notes on relevant [SLURM](https://researchcomputing.princeton.edu/support/knowledge-base/slurm) script parameters, which will be defined in more detail in the slurm section of this workshop.

We will provide a brief introduction to the hardware and terms relevant for parallel computing, along with an overview of four common methods of parallelism. This guide will not teach *how* to implement parallelism in your code but rather will point to the relevant resources for learning.


## Serial versus Parallel Programming

### Serial Programming

Serial programming is the default method of code execution. Serial code is run sequentially; meaning, only one instruction is processed at a time.

<figure>
  <img src="https://hpc.llnl.gov/sites/default/files/serialProblem.gif" alt="One problem block that is worked on in smaller pieces, one at a time." width=50%/>
</figure>

### Parallel Programming

Parallel programming involves breaking up code into smaller tasks or chunks that can run simultaneously.

<figure>
  <img src="https://hpc.llnl.gov/sites/default/files/parallelProblem.gif" alt="One big problem block is split into four medium blocks, and each medium block is worked on in smaller pieces, but at the same time." width=50%/>
</figure>

*Images sourced from Lawrence Livermore National Laboratory's Introduction to [Parallel Computing Tutorial](https://hpc.llnl.gov/training/tutorials/introduction-parallel-computing-tutorial).*

## Brief Introduction to Relevant Vocabulary

### Computer Hardware (CPUs, GPUs, and Memory)

**CPU-chip** – CPU stands for Central Processing Unit. This is the computer's main processing unit; you can think of it as the 'brain' of the computer. This is the piece of hardware that performs calculations, moves data around, has access to the memory, etc. In systems such as [Princeton's High Performance Computing clusters](https://researchcomputing.princeton.edu/systems/systems-overview), CPU-chips are made of multiple CPU-cores.

**CPU-core** – A microprocessing unit on a CPU-chip. Each CPU-core can execute an independent set of instructions from the computer.

**GPU** – GPU stands for the Graphics Processing Unit. Originally intended to process graphics, in the context of parallel programming this unit can do a large number of simple arithmetic computations.

**Memory** – In this guide memory refers to Random-Access Memory, or RAM. The RAM unit stores the data that the CPU is actively working on.

| <a href="https://spectrum.ieee.org/why-cpu-frequency-stalled"><img src="https://spectrum.ieee.org/media-library/eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpbWFnZSI6Imh0dHBzOi8vYXNzZXRzLnJibC5tcy8yNTU2MDE3Ni9vcmlnaW4uanBnIiwiZXhwaXJlc19hdCI6MTYzMDUyMjA4Nn0.HxFJ9q5nmiY4ITML52e-yQZolSnspmb3S0YDhEQzJzQ/image.jpg?quality=80&width=734" alt="Intel chip with four cores." width="300"/></a><p>Source: https://spectrum.ieee.org/why-cpu-frequency-stalled</p>|
|:-----:|
|*A computer's processing power comes from its CPU-chip, or the Central Processing Unit. These days, faster CPU's are made by placing multiple mini-processors (also known as CPU-cores) on one CPU-chip. The CPU-chip in this image contains 4 CPU-cores. Princeton's Research Computing clusters use multi-core computers, with 32-128 cores per compute node.*|

### Additional Parallelism Terminology

An understanding of **threads** and **processes** is also useful when discussing parallel programming concepts.

If you consider the code you need to run as one big job, to run that code in parallel you'll want to divide that one big job into several, smaller *tasks*^[Note that in SLURM scripts, the word task can be used to refer to a process.] that can be run at the same time. This is the general idea behind parallel programming.

When tasks are run as **threads**, the tasks all share direct access to a common region of memory. The mulitple threads are considered to belong to one process.

When tasks run as distinct **processes**, each process gets its own individual region of memory–even if run on the same computer.

To put it even more simply, processes have their own memory, while threads belong to a process and share memory with all of the other threads belonging to that process.

<!--- Current attempt for images & captions. Works, but a hack. Probably not consdiered accessible. --->
|<img src="diagrams/comp.png" alt="One empty rectangle."/>|<img src="diagrams/comp_threads.png" alt="One rectangle, inside of which is a small circle representing one process. There are four separate lines stemming from the circle, representing four threads."/>|<img src="diagrams/comp_processes.png" alt="One rectangle, inside of which are two small circles representing two processes. There is one  line stemming from each circle, representing one thread per process."/>|
|----|----|----|
|If a box represents a computer,|and a task can be represented as line stemming from a spot in memory, then tasks run as threads can be represented as the above, where all threads have access to the same memory space,|and tasks run as processes can be represented as the above, where each process has its own siloed memory.|

<!--- Initial attempt for images & captions. Gives a weird spacing that makes one image's caption look like it belong to another image. --->
<!---
<figure>
  <img src="diagrams/comp.png" alt="One empty rectangle." width=30%/>
  <figcaption>If a box represents a computer,</figcaption>
</figure>

<br/>

<figure>
  <img src="diagrams/comp_threads.png" alt="One rectangle, inside of which is a small circle representing one process. There are four separate lines stemming from the circle, representing four threads." width=30%/>
  <figcaption>and a task can be represented as line stemming from a spot in memory, then tasks run as threads can be represented as the above, where all threads have access to the same memory space,</figcaption>
</figure>

<br>

<figure>
  <img src="diagrams/comp_processes.png" alt="One rectangle, inside of which are two small circles representing two processes. There is one  line stemming from each circle, representing one thread per process." width=30%/>
  <figcaption>and tasks run as processes can be represented as the above, where each process has its own siloed memory.</figcaption>
</figure>
--->

## Four Basic Types of Parallel Programming

The diagrams used in the following sections can be read according to this key diagram. Gray text in the diagram indicates the corresponding SLURM script parameter for each term. (Note that SLURM will be covered in more detail later in the course. We recommend re-visiting the SLURM parameters in these diagrams after reading the SLURM section.)

<figure>
  <img src="diagrams/key.png" alt="Diagram showing a rectangle as a computer, circles inside represent spaces in memory, lines coming from the circle represent threads, and different circles represent different processes that do not share memory." width=50%/>
</figure>


### 1. Embarassingly Parallel

This is the simplest type of parallelism to implement.

A project is embarassingly parallel if each task in a job can be run completely independently of other tasks. In other words, the program runs a bunch of copies of the same task, but each copy has different input parameters.

In embarassingly parallel programs, there is no communication required between tasks, which is what makes it easy to implement.

#### Example SLURM Script  
`--nodes = 1          # node count`  
`--ntasks = 1         # total number of tasks across all nodes`  
`--cpus-per-task = 1  # cpu-cores per task`  
`--array = 0-49       # number of times you'd like your task to run, each time with different input`  

#### Example Diagram   
<figure>
  <img src="diagrams/array.png" alt="One rectangle, inside of which is a small circle representing one process. The rectangle is then repeated 50 times." width=50%/>
</figure>

#### Example Code

<details>
  <summary> Click to expand </summary>

Let's say your project consists of the following data, with one million x-values:

`x = 1, 2, 3, 4, ..., 1000000`

You've written the following program to calculate and print a y-value for each x-value.

`y = (x * 0.3) + 4.21`
`print(y)`

Here, a task is the calcuation of the y-value for one x-value.

Normally, you would run your program serially, meaning you'd use only 1 core to run your program for all the x-values in your data. That one core can only run one task at a time. If, for the sake of simplicity, we say each task takes 1 second to complete, then the program should take

1,000,000 tasks x 1 second/task = 1,000,000 seconds

to complete.

If you run the program in parallel, however, you can now use multiple cores on one computer simultaneously. If, for example, you could have access to 50 cores, then each core can work on a different value of x at the same time. This will cut down the time it takes to complete all tasks to

1,000,000 tasks x 1 second/task % 50 cores = 20,000 seconds


</details>  

</br>

On Princeton's Research Computing clusters, you can run embarassingly parallel programs as [job arrays](https://researchcomputing.princeton.edu/support/knowledge-base/slurm#arrays).

### 2. Shared-Memory Parallelism (Multithreading)

Shared-memory parallelism is when tasks are run as **threads** on separate CPU-cores of the same computer. In other words, a single program can access many cores on one machine.

As the name "shared-memory parallelism" implies, the CPU-cores share memory because they are on the same computer and all have access to the same memory card. Due to the shared memory, a light level of communication is required between the cores working on each task.

Since multiple threads are used to complete a job, shared-memory parallelism is often also refered to as **multithreading**.

A common programming model for shared-memory parallelism is called fork/join. The program starts out with a 'unified' parent thread, and *forks* into multiple child threads which then *join* together again at the end of the program in order to share results with each other.  

#### Methods Associated with Shared-Memory Parallelism

The most common method to implement shared-memory parallelism is **OpenMP**, but there's also POSIX Threads (pthread), SIMD or vector intrinsics (Intel MKL), C++ Parallel STL (Intel TBB), and shmem. These are each specific libaries, and you can choose one that suits your work.


#### Example SLURM Script  
`--nodes = 1          # node count`  
`--ntasks = 1         # total number of tasks across all nodes`  
`--cpus-per-task = 4  # cpu-cores per task (>1 if multi-threaded tasks)  
`
#### Example Diagram  
<figure>
  <img src="diagrams/sharedmem.png" alt="One rectangle, inside of which is a small circle representing one process. There are four separate lines stemming from the circle, representing four threads." width=50%/>
</figure>

#### Example Code
Try running this [OpenMP example](https://github.com/PrincetonUniversity/hpc_beginning_workshop/tree/master/RC_example_jobs/fortran/multithreaded) from our *Getting Started with the Research Computing Clusters* workshop.

### 3. Distributed-Memory Parallelism (Multiprocessing)

Distributed-memory parallelism generally refers to running tasks as multiple **processes** that do not share the same space in memory. While this can technically happen on one computer, that is a more complicated use case. The more intuitive way to understand distributed-memory parallelism is in the case where tasks are run on different computers, as those tasks more obviously have their own memory.

As an example, distributed-memory parallelism could be used to calculate the expected number of people commuting into each USA county per day. To calculate the number of commuters, let's say you require population data from the surrounding counties. The work to calculate commuters by county could be divided up by state, so that a different computer could handle all of the calculations for each state. The counties at the border of each state, however,  need information from the neighboring counties in another state in order to complete their calculations. The process working on New Jersey, for example, would need to communicate with the processes working on the surrounding states (Delaware, Pennsylvania, and New York) to complete its work. In other words, at some point the process on one computer needs to communicate with the processes on other computers in order to finish its tasks.

This is one of the more complicated types of parallelism, since it requires a high level of communication between different tasks to ensure that everything runs properly.

Since multiple processes are needed to complete a job, distributed-memory parallelism is often referred to as **multiprocessing**.

#### Methods Associated with Distributed-Memory Parallelism

The most common method to implement distributed-memory parallelism is **MPI**. MPI is an Application Programming Interface (API) that stands for Message-Passing Interface, and can be used in Fortran, C, and C++/

For those working with machine learning, you may also consider Spark/Hadoop, Dask, and General Multiprocessing.

#### Example SLURM Script  
`--nodes = 3            # node count`  
`--ntasks = 2           # total number of tasks across all nodes`  
`--cpus-per-task = 1    # cpu-cores per task (>1 if multi-threaded tasks)`  

#### Example Diagram  
<figure>
  <img src="diagrams/distmem.png" alt="Three rectangles, representing three computers. Inside of each rectangle are two small circles representing two processes. There's one line stemming from each circle, representing one thread per process.'" width=70%/>
</figure>

#### Example Code
Try running this [MPI example](https://github.com/PrincetonUniversity/hpc_beginning_workshop/tree/master/RC_example_jobs/cxx/parallel) from our *Getting Started with the Research Computing Clusters* workshop.

### 4. Accelerator Parallelism (GPU's, and FPGA's)

Accelerator Parallelism uses different types of computer hardware, such as Graphical Processing Units (GPUs) and Field-Programmable Gate Arrays (FPGAs), to simply do computations faster than any CPU chip is able to. A CPU, for example, can have tens of processing cores, but a GPU has thousands.

To learn more about GPU's, see the [01_what_is_a_gpu](https://github.com/PrincetonUniversity/gpu_programming_intro/tree/master/01_what_is_a_gpu) repository in Research Computing's [*Introduction to GPU Programming* workshop](https://github.com/PrincetonUniversity/gpu_programming_intro) material. You can also check out [Research Computing's Workshops & Live Training](https://researchcomputing.princeton.edu/learn/workshops-live-training) page to see upcoming in-person trainings on GPU topics.

To learn more about FPGA's, check out [Research Computing's Workshops & Live Training](https://researchcomputing.princeton.edu/learn/workshops-live-training) page for upcoming in-person trainings, or search the page for material from past workshops. For example, you can access a recording of [Intel's workshop on FPGAs](https://researchcomputing.princeton.edu/learn/workshops-live-training/archives-past-workshops/spring-2021-workshop-materials) from Spring 2021, or the content from the *Intro to Field-Programmable Gate Arrays (FPGAs)* workshop from Fall 2021.

#### Example Code
Try running several [GPU examples](https://github.com/PrincetonUniversity/gpu_programming_intro/tree/master/03_your_first_gpu_job) from Research Computing's *Introduction to GPU Programming* workshop.

## In Summary

To summarize, the burden of modifying the code to take advantage of multiple cores or multiple nodes is on the programmer. There are multiple types of parallelism to choose from, but just running code on a ‘bigger’ computer doesn’t make it run faster.

## Resource Links to Dive Deeper into Parallel Programming Topics

Resource lists by topic, compiled by staff in Princeton's Research Computing and PICSciE groups:

* [Overview of HPC & Parallel Programming](https://researchcomputing.princeton.edu/education/external-online-resources/hpc-overview)
* [MPI](https://researchcomputing.princeton.edu/education/external-online-resources/mpi)
* [OpenMP](https://researchcomputing.princeton.edu/education/external-online-resources/openmp)

Check Research Computing's upcoming [workshop schedule](https://researchcomputing.princeton.edu/learn/workshops-live-training) for deeper training on Parallel Programming topics. (Workshops on parallel computing are typically held in the Fall semester).

## Online Resources Used to Write This Guide

[A Primer on Parallel Programming](https://princetonuniversity.github.io/PUbootcamp_winter2021/sessions/M2C-parallel-programming/), by Garrett Wright

[University of Oklahoma's Supercomputing in Plain English Workshop Series](http://www.oscer.ou.edu/education.php), by Henry Neeman

[Material for Princeton's R in HPC Workshop](https://github.com/PrincetonUniversity/HPC_R_Workshop), by Ben Hicks

[Parallel Programming Primer I: Fundamental Concepts](https://medium.com/craftdata-labs/parallel-programming-for-data-processing-fundamental-concepts-ab17a3b3d6a9), and [Parallel Programming Primer II: Multiprocessing and Multithreading](https://medium.com/craftdata-labs/parallel-programming-for-data-processing-part-ii-multiprocessing-and-multithreading-8ec8649e9dd1) by Saurav Dhungana
