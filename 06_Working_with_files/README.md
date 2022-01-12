# Working with Files on the Clusters

## The Filesystems

MATILDA includes 690 TB of high-speed scratch storage using a high-performance parallel file system connected via 100Gbps Infiniband to each compute node.


The main file systems on OU's clusters are:

+ `/home/<first-letter-netID>/<YourNetID>`: where you should place source code and executables
+ `/scratch/users/<YourNetID>` : where you should place job output and intermediate results
+ `/projects/<Research Group Name>`: for group work

*IMPORTANT*: Tigress is for non-volatile files only. Do not make the mistake of writing your job output here. Users are tempted to do this because `/tigress` is backed-up while `/scratch/gpfs` is not. However, this is a mistake and you may adversely affect other users by writing or reading job files from `/tigress` during your production runs.
`/home` directories, `/project` space, and shared (at `/cm/shared`) software reside on a Dell EMC Isilon H500, a storage system with integrated backup solution. Data is replicated to a Dell EMC Isilon A2000 located in a secondary data center with independent power and HVAC systems. The Dell EMC Isilon A2000 can also provide an archival mechanism to Amazon Web Services.

Storage details [here](https://kb.oakland.edu/uts/HPCStorage)


### Where Should I Put My Files?

* **Code** -- somewhere under your home-directory tree is ok (if these are smaller files that aren't too huge)
* **Temporary files your code generates while it runs** -- depends whether you want to handle the housekeeping of removing these files yourself or delegate the housekeeping to SLURM:
    + If your script removes temporary files -- save them to `/scratch` (this is a scratch area local to each cluster; not auto-purged, so send "workspace" files here if your script handles deleting them)
    + If you want SLURM to remove temporary files -- save them to `/tmp`. You can change this location by pointing environment variable `TMPDIR` by using `export TMPDIR=/scratch/users/netID/tmp`
* **Output files after code runs** -- `/scratch/users/netID` 

* **Longer term results for archiving** -- move it to `/home` or, if possible, to `/projects` (preferred).  These *are* backed up

Additional information using the file system can be found in HPC's [website](https://kb.oakland.edu/uts/HPCStorage).

The commands below give you an idea of how to properly run a job in terms of suitable locations for file output:

```
$ ssh <YourNetID>@hpc-login.oakland.edu  
$ cd /scratch/users/<YourNetID>  
$ mkdir myjob
$ cd myjob
# put necessary files and Slurm script in myjob
$ sbatch job.slurm
```

If the run produces data that you want to backup, then you must copy or move the data to `/projects` or `/home` after the run:
```
$ cp -r /scratch/users/<YourNetID>/myjob /projects/group

```

## How Do I Get My Files Onto (or Off) the Cluster?

One of the most frequently asked questions is how to get files to Adroit or any other cluster.
Again, Linux/MacOS has an answer out of the box: the `scp` command. For Windows, clients like PuTTY and Mobaxterm
or FTP clients (like [WS_FTP](https://www.ipswitch.com/secure-information-and-file-transfer/wsftp-client)–paid, sadly–or [Filezilla](https://filezilla-project.org/)) are needed. In both cases,
make sure you're using interactive mode. Filezilla especially can be a pain
with Duo Authentication for Nobel, but we have some tips [here](https://askrc.princeton.edu/question/343/how-do-i-get-filezilla-to-work-around-duo/).

If you're transferring a lot of files, consider
zipping them.

The default client to transfer files is `scp`. This is a copy command that uses
SSH to copy files.

The general structure of the scp command is:  
`scp [options] [netid]@source-host:file/location [netid@]destination-host:file/location`

It's generally more straightforward to transfer files from your personal computer to the clusters, since you don't need to specify the user and host for the system you're already in.

### Example - Using scp Command

An OU's student with the netid jessedoe wants to transfer a `data.csv` file from their personal laptop to the `/scratch/users/jessedoe` folder on the MATILDA cluster. To do this, they would use the following command:

`scp ~/mydatafiles/data.csv jessedoe@hpc-login.oakland.edu:/scratch/users/jessedoe`

Transferring a folder requires the -r option, and would therefore look like this:

`scp -r ~/mydatafiles jessedoe@hpc-login.oakland.edu:/scratch/users/jessedoe`  
