# DeepLabCut via Docker

Guide to installing and using DeepLabCut on HPC via Docker.

Pulling the docker image to Discovery
```
srun -p gpu --gres=gpu:1 -N 1 -n 2 --pty /bin/bash

module load singularity

cd /scratch/$USER

mkdir deeplabcut # this can be your desired directory 

cd deeplabcut

mkdir cache

mkdir tmp

export SINGULARITY_CACHEDIR=/scratch/$USER/deeplabcut/cache

export SINGULARITY_TMPDIR=/scratch/$USER/deeplabcut/tmp


# for latest images see the tags tab on DeepLabCut's Docker page:
# https://hub.docker.com/r/deeplabcut/deeplabcut/tags

singularity build deeplabcut.sif docker://deeplabcut/deeplabcut:2.2.1.1-jupyter-cuda11.0.3-runtime-ubuntu18.04
```

Running the Docker Image

open as an interactive shell
```
singularity shell deeplabcut.sif 
```
