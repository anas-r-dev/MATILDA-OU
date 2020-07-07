# OpenACC Example in C

Compile the code with the following commands:

```
$ module load pgi
$ pgcc -g -acc -ta=tesla:cc70 -Minfo=all -o laplace2d_acc laplace2d.c
```

On TigerGPU, use `-ta=tesla:cc60` instead.

Submit the job with:

```
$ sbatch job.slurm
```

The expected output is:

```
Jacobi relaxation Calculation: 2048 x 2048 mesh
    0, 0.250000
  100, 0.002397
  200, 0.001204
  300, 0.000804
  400, 0.000603
  500, 0.000483
  600, 0.000403
  700, 0.000345
  800, 0.000302
  900, 0.000269
 total: 41.560700 s
```