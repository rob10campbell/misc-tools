# How to use the Discovery Research Cluster for GPU software

This file provides a [brief overview](/ARTG-5000/disco-gpu.md#brief-overview-of-discovery) of the Discovery cluster.

It also provides a [list of commands](/ARTG-5000/disco-gpu.md#step-by-step-guide) for:
- accessing Discovery
- switching to a compute node on the GPU partition
- using a container with tensorflow already installed
- installing a new Python conda environment
- running a simulation


## Brief Overview of Discovery
You can connect to Discovery from a Terminal or the web-based Open On Demand (OOD). For more details on this see the Resources list at the end of this section. You can transfer data to and from Discovery using `scp`, FileZilla, OOD, or Globus. For more details on file transfer see the Resources list at the end of this section.

Once you have access to Discovery you can access preexisting nodes, partitions, direcotries, software, and pre-configured software environments (AKA containers) to run code on either CPUs or GPUs.

### NODES
When you log in to Discovery you are brought to your user account's "home directory" on the "login node."  Before running a job you must switch to a "compute node" using the `srun` command (or by submitting a job to run in the background, using a bash script with `sbatch`). 

### PARTITIONS
For CPU jobs you should use nodes on the `short` partition (max 24hrs). For GPU jobs you should use nodes on the `gpu` partition (max 8hrs). 

### DIRECTORIES
Because of the limited resources available in the "home directory," the best practice is to also move to your user account's folder in the "scratch directory" (`/scratch/your.username/`).

### SOFTWARE
Discovery has many software packages already installed. You can access exisiting software by loading a "module"
```
# view all modules with module avail
module avail

# load a module with module load
module load module-name

# view which modules are loaded using module list
module list

# unload a module with module unload
module unload module-name
```

### PRECONFIGURED SOFTWARE ENVIRONMENTS (CONTAINERS)
Often software that is optimized for GPUs requires specific settings that best match the hardware. To save time you can access pre-exisiting software environments that already work on Discovery. These environments are saved as "containers."

To access Discovery's list of currently available containers you will use the singularity module and Northeastern's Harbor Registry.

To get access, first login [Northeastern's Harbor Registry here](to https://containers.rc.northeastern.edu/) using your NU username and password to obtain an account. If you want to create your own "projects" on Harbor for sharing with other users, contact RC-help to get access to "projects" within Harbor.

### RESOURCES
A brief video introducing Discovery and how to get started is available on the [Discovery Trainings website](https://rc.northeastern.edu/support/training/).

More advanced topics are covered in videos and slides available through Canvas.

The [Discovery Docs](https://rc-docs.northeastern.edu/en/latest/welcome/welcome.html) contain detailed descriptions of all of these features. Here are some useful specific links:

- Steps for how to connect to Discovery are available for:
	-  [Mac](https://rc-docs.northeastern.edu/en/latest/first_steps/connect_mac.html)
	- [Windows](https://rc-docs.northeastern.edu/en/latest/first_steps/connect_windows.html)
	- web-based [Open-On Demand](https://rc-docs.northeastern.edu/en/latest/first_steps/connect_ood.html).

- More information on the web-based Open-On Demand is available [in the Discovery docs here](https://rc-docs.northeastern.edu/en/latest/using-ood/introduction.html)

- More information about how to transfer files is available for: 
	- [the scp command](https://rc-docs.northeastern.edu/en/latest/using-discovery/transferringdata.html)
	- the "upload" and "download" buttons [on OOD's File Explorer](https://rc-docs.northeastern.edu/en/latest/using-ood/fileexplore.html)
	- [FileZilla](https://rc-docs.northeastern.edu/en/latest/using-discovery/transferringdata.html?highlight=filezilla#data-transfer-node-using-windows) (listed for Windows but also available on Mac)
	- [Globus](https://rc-docs.northeastern.edu/en/latest/using-discovery/globus.html) (the most complicated to set up)

- More information about partitions (`short` and `gpu` and others) is available [in the Discovery docs here](https://rc-docs.northeastern.edu/en/latest/hardware/partitions.html)

- More information about the `srun` command is available [in the Discovery docs here](https://rc-docs.northeastern.edu/en/latest/using-discovery/srun.html)


## Step-by-step Guide

Step 1: Login to Discovery from a Terminal on Mac
```
ssh -Y your.username@login.discovery.neu.edu
```

Step 2: Move to a GPU compute node (duration: 2hrs)
```
srun -p gpu --gres=gpu:t4:1 -N 1 -n 2 --pty --time=02:00:00 /bin/bash
```
You will see output that your job is queued, and again when you are allocated resources, before switching to the new node. You can tell which node you are on by looking at the the path in your Terminal prompt. It should have changed from a login node ("login-00") to a letter and number combination (usually starting with "c" or "d," for example, "d1005") representing the compute node you have been assigned.
```
[your.username@login-00 ~]$ srun -p gpu --gres=gpu:t4:1 -N 1 -n 2 --pty --time=02:00:00 /bin/bash
srun: job 32862421 queued and waiting for resources
srun: job 32862421 has been allocated resources
[your.username@d1005 ~]
```

Step 3: Load the singularity module for working with containers
```
module load singularity
```

Step 4: Create a new directory on Scratch to run your simulations
```
mkdir /scratch/$USER/ARTG-5000/
cd /scratch/$USER/ARTG-5000/
```

Step 5: Copy your files to the ARTG-5000 folder with `scp` (for information on other file transfer methods see the Resources section, above)<br>
**Open a new Terminal window** and cd to the directory where your files are stored
```
cd my-path-to-files
```
Then use the `scp` command to transfer your files to Discovery **remember to replace "filename1" "filename2" etc. with the name of your files and "your.username" with your Northeastern username**
```
scp filename1 filename2 your.username@xfer.discovery.neu.edu:/scratch/your.username/ARTG-5000
```
**Once the transfer is complete, close the new Terminal window and switch back to the original window that is logged into Discovery**

Step 6: Start an interactive session with the container
```
singularity run --nv -B /scratch/$USER:/mnt /shared/container_repository/tensorflow/tensorflow_22.05-tf2-py3.sif /bin/bash
```

You can now access and run files inside your user's `scratch` directory from the mounted container. Check which files are avaialable to you here using the command
```
ls /mnt
```

Step 7: Run your scripts
```
python my-training-script.py
```

Step 8: Copy output data to your local computer using `scp` or another method<br>
**Open a new Terminal window**
```
cd my-path-to-files
scp your.username@xfer.discovery.neu.edu:/scratch/your.username/ARTG-5000/filename .
```

Step 9:
**In the Terminal window where you are logged into Discovery**

To exit the container, use the command
```
exit
```

To end your srun session on a compute node (if it has not already ended), use the command
```
exit
```

To logout of Discovery, use the command
```
logout
```
