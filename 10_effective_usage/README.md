# Effective Usage of the Cluster Resources

Here are the key pieces:

- Number of CPU-cores  
- Amount of time required to run the job  
- Amount of memory (RAM) needed  
- Number of GPUs (if any)  


## How to Find the Optimal Number of Threads for Multithreaded Codes

### Multi-threaded NumPy

Try running the code below and vary the value of `--cpus-per-task` to fill in the data in the table below:

```python
import os
num_threads = int(os.environ['SLURM_CPUS_PER_TASK'])

import mkl
mkl.set_num_threads(num_threads)

N = 2000
num_runs = 5

import numpy as np
np.random.seed(42)

from time import perf_counter
x = np.random.randn(N, N).astype(np.float64)
times = []
for _ in range(num_runs):
  t0 = perf_counter()
  u, s, vh = np.linalg.svd(x)
  elapsed_time = perf_counter() - t0
  times.append(elapsed_time)

print("execution time: ", min(times))
print("cpus-per-task (or threads): ", num_threads)
print("trace(s): ", s.sum())
```

Here is an appropriate Slurm script:

```bash
#!/bin/bash
#SBATCH --job-name=py-svd        # create a short name for your job
#SBATCH --nodes=1                # node count
#SBATCH --ntasks=1               # total number of tasks across all nodes
#SBATCH --cpus-per-task=1        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --mem-per-cpu=1G         # memory per cpu-core
#SBATCH --time=00:01:00          # total run time limit (HH:MM:SS)

hostname
lscpu | grep "Model name"

module load miniconda3

srun python svd_np.py
```

The following data was found using one of the Skylake nodes on Adroit:

| cpus-per-task (or threads)| execution time (s) | speed-up ratio |  parallel efficiency |
|:--------------------------:|:--------:|:---------:|:-------------------:|
| 1                          |  4.2     |     1.0   |   100%              |
| 2                          |  2.2     |   1.9     |   95%               |
| 4                          |  1.6     |   2.6     |   66%               |
| 8                          |  0.78    |   5.4     |   67%               |
| 16                         |  0.65    |   6.5     |   40%               |
| 32                         |  0.71    |   5.9     |   18%               |

We see that by dividing the computation across several threads, which run on the CPU-cores, the execution time
is dramatically reduced. What is the best choice for `--cpus-per-task` for this case? Clearly, using 32 CPU-cores does not lead to optimal performance.

A similar procedure can be used to find the optimal number of CPU-cores for MPI codes and the optimal number of GPUs for codes that use GPUs.
