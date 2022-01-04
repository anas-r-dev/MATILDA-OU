# What's a High Performance Computing (HPC) cluster?

Back in the day, the machines used for high performance computing were known as "supercomputers": monolithic standalone machines
with specialized hardware–very different from what you would find in
home and office computers. Nowadays, the majority of supercomputers
are instead **computer clusters** (or just "**clusters**" for short) ---
collections of (comparatively) low-cost standalone computers that are
networked together and endowed with software to coordinate programs on
(or across) those computers.

| <img src="images/hpcrc_openhouse_20111129_fac_2475.jpg" alt="Racks of computers that make up a supercomputer." width="400"/>| <img src="images/hpcrc_nodes.JPG" alt="The backend of a rack of computers with wire connectors between coming out of various places." width="400"/> | <img src="images/connections_byKevinAbbey_20210519_a.jpg" alt="The backend of a rack of computers with wire connectors between coming out of various places." height="250"/>|
|----|----|----|
| *Racks of computers in the data center.* | *Multiple computers stacked in a rack.* | *Backend of computer racks, networked together.*|

<!---

<figure>
  <img src="https://upload.wikimedia.org/wikipedia/commons/2/29/Virginia_tech_xserve_cluster.jpg" alt="Racks of computers that make up a supercomputer." width="300"/>
  <figcaption>Racks of computers in a data center.</figcaption>
</figure>

<br/>

<figure>
  <img src="connections_byKevinAbbey_20210519_a.jpg" alt="The backend of a rack of computers with wire connectors between coming out of various places." width="300"/>
  <figcaption>Backend of computer racks.</figcaption>
</figure>

--->

The computational systems made available by Princeton Research
Computing are, for the most part, *clusters*.  Each computer in the
cluster is called a **node**, and we commonly talk about two types of nodes: **head node** and **compute nodes**.

|![Diagram with Terminology](images/beowulf.png)|
|:--:|
|*Architecture of a Oakland University Matilda High Performance cluster.*|

## Terminology

* **Login Node** - The login node is the computer where we land when we log in to the cluster. This is where we edit scripts, compile code, and submit jobs to the scheduler. The login nodes are shared with other users and jobs should not be run on the login nodes themselves.
* **Standard Compute Nodes** - The compute nodes are the computers where jobs should be run. In order to run jobs on the compute nodes we must go through the job scheduler. By submitting jobs to the job scheduler, the jobs will automatically be run on the compute nodes once the requested resources are available. Matilda uses SLURM as their scheduling program and we will get back to this in a later section. There are 40 compute nodes on Matilda
* **High Throughout nodes** - These nodes have 8 cores (40 as compared with standard compute nodes) but have more clock speeds (3.80 GHz). There are 10 throughput nodes on Matilda
* **Big Memory Nodes** - These nodes have more memory than throughput and compute nodes. 768 GB as compared to 192 GB for compute/throughput/hybrid nodes. Matilda has 4 Big Memory Nodes
* **Large Memory Node** - Matilda has one large memory node with 1.5 TB of memory.
* **Hybrid Nodes** - These nodes have capability to add GPUs. But currently they are same as standard compute nodes.
* **GPU Nodes** - Have 4 NVIDIA Tesla V100 16G GPUs
* **Cores** - A shorthand way to refer to the number of processor cores (usually physical) of a CPU in a node.

## How Does Matilda Work?

To have your program run on the clusters, you can start a **job** on the head node. A job consists of the the following files:
1. your code that runs your program
2. a separate script, known as a SLURM script, that will request the resources your job requires in terms of the amount of memory, the number of cores, number of nodes, etc. As mentioned previously, Princeton uses a scheduler called **SLURM**, which is why this script is referred to as your SLURM script.

Once your files are submitted, the **scheduler** takes care of figuring out when the resources you requested will become available on the compute nodes. Once resources become available, the scheduler runs your program on the compute nodes.
