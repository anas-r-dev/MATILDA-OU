# Connecting to the Clusters via SSH

## A Few Caveats
These instructions assume you:
1. Are Oakland University faculty, student, or staff
2. Are connecting with **Duo Authentication**
    * Matilda requires require two-factor authentication via Duo from off campus. 
    * Upon connecting, you can request a push to a cell phone application, a text with a passcode, or you can enter a generated pass code with a soft key created by the Duo application on your cell phone.
3. Already have an access to MATILDA. You can request access from "Matilda HPC Cluster Access Request"  [here](https://oakland.edu/uts/efficient-processes-forms/forms/).

## SSH

In order to connect to MATILDA, you will need an SSH
(secure shell) client. 

On MacOS and Linux, the default Terminal application has such a client built-in.
No download is necessary!

On Windows machines, you'll need a client. One option is [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) and another is [Mobaxterm](http://mobaxterm.mobatek.net/). Note: ssh is now present in Windows CMD too!

You can access Matilda via netid@hpc-login.oakland.edu

### Using Terminal as an Example

Once you launch an instance of Terminal, you'll be at a command prompt *on your local
machine* (i.e., on your computer) that looks something like this:
```
user@hostname:~$
```
The `$` is an indication that you're ready to enter a command.

To connect to a HPC, the general address looks like this, where you replace the <netID>'s with your netID:

```
ssh <netID>@hpc-login.oakland.edu
```

 and after hitting Enter, You'd see something like this
```
Warning: the ECDSA host key for 'hpc-login.oakland.edu' differs from the key for the IP address '128.112.128.32'
Offending key for IP in /Users/bhicks/.ssh/known_hosts:88
Matching host key in /Users/bhicks/.ssh/known_hosts:115
Are you sure you want to continue connecting (yes/no)? yes
netID@hpc-login.oakland.edu's password: 
Duo two-factor login for netID

Enter a passcode or select one of the following options:

 1. Duo Push to +XX XXX XXX1239
 2. SMS passcodes to +XX XXX XXX1239

Passcode or option (1-2): 1

Pushed a login request to your device...
Success. Logging you in...
     __  ___        __   _  __     __          __  __ ____   ______ ______  
    /  |/  /____ _ / /_ (_)/ /____/ /____ _   / / / // __ \ / ____// ____/  
   / /|_/ // __ `// __// // // __  // __ `/  / /_/ // /_/ // /    / /       
  / /  / // /_/ // /_ / // // /_/ // /_/ /  / __  // ____// /___ / /___     
 /_/  /_/ \__,_/ \__//_//_/ \__,_/ \__,_/  /_/ /_//_/     \____/ \____/     
 
This system is for authorized users only.
Your use of this system indicates your agreement to Oakland
University's policies and consent to monitoring, recording, and
auditing of usage. Unauthorized use of this system is prohibited
and subject to discipline and prosecution.
 
 The Matilda HPCC utilizes the Slurm workload schedule,
   see: https://slurm.schedmd.com/quickstart.html 
 To see a list of available modules/software use: modules avail
 To load software to your path use: module load <module_name>
 
 To avoid 'no space left on device' errors during runs please consider
 reassigning your TMP space: https://kb.oakland.edu/uts/HPCtmp
 
Join us on Slack
Sign up here: https://join.slack.com/t/ouhpc/shared_invite/zt-10w1e2b35-1GZDLq_iiIRlUTI3ldqoJg
[netID@hpc-login-p01 ~]$

```

You're now remotely connected to MATILDA!

The shell I used in both cases is one called [Bash](https://www.gnu.org/software/bash/).
It's a particular command line interface that is common across Unix-alike machines.

 ## SSH Keys: `ssh` without typing passwords

 Typing passwords every time you want to connect to a machine or, more annoyingly, every time you want to copy a file to/from a remote machine gets annoying quickly.  One solution is to enable passwordless login/remote operations by generating a public/private pair of *ssh keys* and using them to negotiate the connection.  The procedure is explained in [this guide](https://github.com/PrincetonUniversity/removing_tedium/tree/master/02_passwordless_logins).

 ## <a name="tmux">Staying connected (tmux)</a>

 If your shell is disconnected, the command you're running terminates. This comes up very frequently if your internet connection is unstable, or your tasks are
 run directly from the command line rather than the SLURM scheduler. This is bad.
 A built-in solution to most Unix-alike operating systems is `nohup`, but it's of limited utility.

 Hence we bring in something like [tmux](https://www.ocf.berkeley.edu/~ckuehl/tmux/).
 It comes installed on all university clusters, and it lets you start a shell
 session that, rather than being remote via SSH, lives on the server.

 A simple use case would be

 ```
 # To launch tmux
 tmux
 tmux attach

 # To detach from your session
 # Press ctrl-b, then d

 # To kill your session
 # Press ctrl-d during the tmux session
 ```

 You would then be attached back to the session you created by default when you called tmux.
 To close a session,
 just `exit` or `ctrl+d`. These sessions will remain until the server is rebooted or you
 close them. Anything you run while attached to the session runs in that session,
 and therefore it is safe from a disconnect.


 `tmux` is a powerful and complex tool. In addition to the simple guide linked above,
 you might see the following:

 - [tmux - a very simple beginner's guide](https://www.ocf.berkeley.edu/~ckuehl/tmux/)
 - [Beginner’s Guide to Tmux ](https://www.codementor.io/bruno/beginner-s-guide-to-tmux-recommended-configuration-plugins-and-navigation-demo-aih7o7ktw) (Feel free to ignore the installation as it's already on
 the clusters, unless you want to run this on your Mac!)
 - [A tmux primer](https://danielmiessler.com/study/tmux/)


 ## Learn More About MATILDA by Running Commands

 Once in login node, type each command below and examine the output:

 ```
 hostname                  # get the name of the machine you are on
 whoami                    # get username of the account
 date                      # get the current date and time
 pwd                       # print working directory
 cat /etc/os-release       # info about operating system
 lscpu                     # info about the CPUs on head node
 sinfo -N                    # info about the nodes (7 nodes for myadroit)
 squeue                    # which jobs are running or waiting to run
 who                       # list users on the head node
 ```

 Here is example output from the commands above:

 <pre>
 $ <b>hostname</b>
 hpc-login-p01


 $ <b>whoami</b>
 mraza


 $ <b>date</b>
 Fri Feb 21 16:20:03 EST 2020


 $ <b>pwd</b>
 /home/m/mraza/


 $ <b>cat /etc/os-release</b>
   NAME="Red Hat Enterprise Linux"
   VERSION="8.3 (Ootpa)"
   ID="rhel"
   ID_LIKE="fedora"
   VERSION_ID="8.3"
   PLATFORM_ID="platform:el8"
   PRETTY_NAME="Red Hat Enterprise Linux 8.3 (Ootpa)"
   ANSI_COLOR="0;31"
   CPE_NAME="cpe:/o:redhat:enterprise_linux:8.3:GA"
   HOME_URL="https://www.redhat.com/"
   BUG_REPORT_URL="https://bugzilla.redhat.com/"

   REDHAT_BUGZILLA_PRODUCT="Red Hat Enterprise Linux 8"
   REDHAT_BUGZILLA_PRODUCT_VERSION=8.3
   REDHAT_SUPPORT_PRODUCT="Red Hat Enterprise Linux"
   REDHAT_SUPPORT_PRODUCT_VERSION="8.3"


 $ <b>lscpu</b>
 Architecture:        x86_64
CPU op-mode(s):      32-bit, 64-bit
Byte Order:          Little Endian
CPU(s):              40
On-line CPU(s) list: 0-39
Thread(s) per core:  1
Core(s) per socket:  20
Socket(s):           2
NUMA node(s):        4
Vendor ID:           GenuineIntel
CPU family:          6
Model:               85
Model name:          Intel(R) Xeon(R) Gold 6248 CPU @ 2.50GHz
Stepping:            7
CPU MHz:             1801.465
CPU max MHz:         3900.0000
CPU min MHz:         1000.0000
BogoMIPS:            5000.00
Virtualization:      VT-x
L1d cache:           32K
L1i cache:           32K
L2 cache:            1024K
L3 cache:            28160K
NUMA node0 CPU(s):   0,4,8,12,16,20,24,28,32,36
NUMA node1 CPU(s):   1,5,9,13,17,21,25,29,33,37
NUMA node2 CPU(s):   2,6,10,14,18,22,26,30,34,38
NUMA node3 CPU(s):   3,7,11,15,19,23,27,31,35,39
Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid dca sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb cat_l3 cdp_l3 invpcid_single intel_ppin ssbd mba ibrs ibpb stibp ibrs_enhanced fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpcid cqm mpx rdt_a avx512f avx512dq rdseed adx smap clflushopt clwb intel_pt avx512cd avx512bw avx512vl xsaveopt xsavec xgetbv1 xsaves cqm_llc cqm_occup_llc cqm_mbm_total cqm_mbm_local dtherm ida arat pln pts pku ospke avx512_vnni md_clear flush_l1d arch_capabilities


 $ <b>sinfo</b>
 PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
defq*        up 7-00:00:00      9    mix hpc-bigmem-p[01,03-04],hpc-compute-p[04,36,40],hpc-gpu-p[01-03]
defq*        up 7-00:00:00      5  alloc hpc-compute-p[24-26,28-29]
defq*        up 7-00:00:00     48   idle hpc-bigmem-p02,hpc-compute-p[01-03,05-23,27,30-35,37-39],hpc-hybrid-p[01-04],hpc-largemem-p01,hpc-throughput-p[01-10]

 $ <b>squeue -u $USER #To see your jobs</b>
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
             34486      defq     bash    mraza  R    2:42:06      1 hpc-gpu-p01


 $ <b>who</b>
  bill  pts/0        Jan  6 13:55 (142.213.134.27)
  john  pts/1        Jan 12 06:31 (141.200.154.222)
  mraza  pts/2        Jan 10 13:29 (141.210.134.136)

 </pre>
