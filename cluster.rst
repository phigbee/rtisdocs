Slurm Job Scheduling 
======================================

Resource Guide
--------------

Otago cluster compute and GPU resources are accessible via ssh or via a web
browser. 
These resources are managed by Slurm job scheduling system.

How to get access
^^^^^^^^^^^^^^^^^
Email `rtis.solutions@otago.ac.nz <mailto:rtis.solutions@otago.ac.nz>`_ to get
your Otago account enabled.


Connecting 
----------
There are two ways to access the login nodes. 
Both require you to either be on the Otago campus or connected to the Otago
network via VPN. 
directly:    

  * Use SSH to log in: ``ssh <otago-username>@aoraki-login.otago.ac.nz``  
  * Use the `Otago OnDemand web portal <http://ondemand.otago.ac.nz>`_.
    See Ondemand instructions at the 
    `OnDemand documentation
    <https://rtis.cspages.otago.ac.nz/research-computing/cluster/ood.html#>`_ 
    page. 


Slurm Overview
--------------
The Otago cluster uses **S**\ imple **L**\ inux **U**\ tility for **R**\ esource
**M**\ anagement (SLURM) for job management.
Slurm is an open-source resource manager and job scheduling system for HPC
(**H**\ igh-\ **P**\ erformance **C**\ omputing) which manages jobs, job steps,
nodes, partitions (groups of nodes), and other entities on the cluster.
Its main purpose is to allocate and manage resources on a cluster, so that jobs
can be run in a way that maximizes utilization of the available resources.

Slurm Workflow
---------------
What follows is a high-level overview of how Slurm works:

  1. Users submit jobs to Slurm using the ``sbatch`` command. A job is a set of
     instructions for running a particular program or set of programs.
  2. Slurm assigns resources to the job based on the requested resources and
     the availability of resources on the cluster. 
     This includes CPU cores, memory, GPUs, and other resources.
  3. Slurm creates a job step for each task in the job. 
     A job step is a subunit of a job that runs on a single node.
  4. Slurm schedules the job steps to run on the assigned resources. 
     It takes into account the job's dependencies, priorities, and other 
     factors to ensure that jobs run in the most efficient way possible.
  5. Once a job step is running, Slurm monitors it and manages it throughout 
     its lifecycle. 
     This includes managing I/O, handling errors, and collecting job
     statistics.
  6. When a job is complete Slurm notifies the user and provides them with the
     output and any error messages that were generated.

Slurm provides a powerful set of tools for managing large-scale compute
resources which allows users to run complex simulations and data analyses more
efficiently and effectively.

.. hint:: Finding Output

  Output from running a SLURM batch job is, by default, placed in a file named
  ``slurm-%j.out``, where the job's ID is substituted for ``%j``; e.g.
  ``slurm-478012.out``.
  This file will be created in your current directory; i.e. the directory from
  within which you entered the ``sbatch`` command.
  Also by default, both command output and error output (to stdout and stderr,
  respectively) are combined in this file.

  To specify alternate files for command and error output use:

  ``--output``
    destination file for stdout
  ``--error``
    destination file for stderr

Basic Slurm Commands
--------------------
There are several basic SLURM commands you'll likely use often:

   ``sinfo``
     View the status of the cluster's compute nodes.
     The output includes how many nodes and of what types are currently 
     available for running jobs
   ``squeue``
     Check the current jobs in the batch queue system. 
     Use ``squeue -u $USER`` to view your own jobs.
   ``sbatch``
     Submit a job to the batch queue system.
     Use ``sbatch myjob.sh`` to submit the Slurm job script ``myjob.sh``.   
   ``srun``
     Submit an interactive job to the batch queue system .
   ``scancel``
     Cancel a job based on its job ID. 
     ``scancel 123`` would cancel the job with ID ``123``.

.. hint::
   ``sinfo`` will quickly tell you the state of the cluster and ``squeue`` 
   will show you all of the jobs running and in the queue. 



Submitting Jobs
---------------
Here we give details on job submission for various kinds of jobs in both batch
(i.e., non-interactive or background) mode and interactive mode.

In addition to the key options of account, partition, and time limit (see
below), your job script files can also contain options to request various
numbers of cores, nodes, and/or computational tasks. 
There are also a variety of additional options you can specify in your batch
files, if desired, such as email notification options when a job has completed.
These are all described further below.

Key Options for Job Submissions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
when submitting a job the three key options required are 

 1. the account you are submitting under, 
 2. the partition you are submitting to, and 
 3. a maximum time limit for your job.

Here are more details on the key options:

Batch Jobs
''''''''''
When you want to run one of your jobs in batch (i.e. non-interactive or
background) mode, you'll enter an ``sbatch`` command. 
As part of that command, you will also specify a SLURM job script file, e.g.
``sbatch myjob.sh``

A job script specifies where and how you want to run your job on the cluster
and ends with the actual command(s) needed to run your job.
The job script file looks much like a standard shell script (``.sh``) file, but also includes one or more lines that specify options for the SLURM scheduler.
These lines take the form of

.. code-block:: bash

  #SBATCH --some-option-here

Although these lines start with hash signs (``#``), and thus are regarded as
comments by the shell, they are nonetheless read and interpreted by the SLURM
scheduler.

Slurm Scheduler Example
^^^^^^^^^^^^^^^^^^^^^^^

Here is a minimal example of a job script file. 
It will run unattended for up to 30 seconds on one of the compute nodes in the
``aoraki`` partition, and will simply print out the text ``hello world``.


.. code-block:: bash

    #!/bin/bash
    # Job name:
    #SBATCH --job-name=test
    #
    # Account:
    #SBATCH --account=account_name
    #
    # Partition:
    #SBATCH --partition=aoraki
    #
    # Request one node:
    #SBATCH --nodes=1
    #
    # Specify one task:
    #SBATCH --ntasks-per-node=1
    #
    # Number of processors for single task needed for use case (example):
    #SBATCH --cpus-per-task=4
    #
    # Wall clock limit:
    #SBATCH --time=00:00:30
    #
    echo "hello world"  

If the text of this file is stored in ``hello_world.sh`` you could run and
retrieve the result at the Linux prompt as follows

.. code-block:: 

  $ sbatch hello_world.sh
  Submitted batch job 716
  $ cat slurm-716.out
  hello world

.. note::

  By default the output will be stored in a file called ``slurm-<number>.out``
  where ``<number>`` is the job ID assigned by Slurm

Interactive Jobs
''''''''''''''''
In some instances, you may need to use software that requires user interaction
rather than running programs or scripts in batch mode. 
To do so, you must first start an instance of an interactive shell on a Otago
login node, within which you can then run your software on that node.
To run such an interactive job on a compute node, you'll use ``srun``. 
Here is a basic example that launches an interactive bash shell on that node
and includes the required account and partition options:

.. code-block::

    [user@aoraki01 ~]$ srun --pty --account=account_name --partition=aoraki --time=00:00:30 --input=stdin bash

Once your job starts, the prompt will change and indicate you are on a compute
node rather than a login node:

.. code-block:: bash

    srun: job 669120 queued and waiting for resources  
    srun: job 669120 has been allocated resources  
    [user@aoraki01 ~]


Job Submission With Specific Resource Requirements
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
This section details options for specifying the resource requirements for you
jobs. 
We also provide a variety of example job scripts for setting up
parallelization, low-priority jobs, jobs using fewer cores than available on a
node, and long-running FCA jobs.

Remember that nodes are assigned for exclusive access by your job, except in
the "Otago2_htc" and "Otago2_gpu" partitions.
So, if possible, you generally want to set SLURM options and write your code to
use all the available resources on the nodes assigned to your job (e.g. 20
cores and 64 GB memory per node in the "Otago" partition).

Memory Available
^^^^^^^^^^^^^^^^
Also note that in all partitions except for GPU and HTC partitions, by default
the full memory on the node(s) will be available to your job. 
On the GPU and HTC partitions you get an amount of memory proportional to the
number of cores your job requests relative to the number of cores on the node.
For example, if the node has 64 GB and 8 cores, and you request 2 cores, you'll
have access to 16 GB memory.
If you need more memory than that, you should request additional cores. 

Parallelization
'''''''''''''''
When submitting parallel code, you usually need to specify the number of tasks,
nodes, and CPUs to be used by your job in various ways.
For any given use case, there are generally multiple ways to set the options to
achieve the same effect; these examples try to illustrate what we consider to
be best practices.

The key options for parallelization are:

    ``--nodes`` (or ``-N``)
      indicates the number of nodes to use
    ``--ntasks-per-node``
      indicates the number of tasks (i.e., processes one wants to run on each
      node)
    ``--cpus-per-task`` (or ``-c``)
      indicates the number of cpus to be used for each task

In addition, in some cases it can make sense to use the ``--ntasks`` (or
``-n``) option to indicate the total number of tasks and let the scheduler
determine how many nodes and tasks per node are needed.
In general ``--cpus-per-task`` will be ``1`` except when running threaded code.  

Note that if the various options are not set SLURM will in some cases infer
what the value of the option needs to be given other options that are set and
in other cases will treat the value as being ``1``. 
So some of the options set in the example below are not strictly necessary, but
we give them explicitly to be clear.

Here's an example script that requests an entire Otago node and specifies 20
cores per task.

.. code-block:: bash

    #!/bin/bash
    #SBATCH --job-name=test
    #SBATCH --account=account_name
    #SBATCH --partition=aoraki
    #SBATCH --nodes=1
    #SBATCH --ntasks-per-node=1
    #SBATCH --cpus-per-task=20
    #SBATCH --time=00:00:30
    ## Command(s) to run:
    echo "hello world" 
    
Only the partition, time, and account flags are required.

GPU Jobs
---------

Please see example job script for such jobs.

The key things to remember are:

 * Submit to a partition with nodes with GPUs
 * Include the ``--gres`` flag.
 * Request at least two CPUs for each GPU requested, using ``--cpus-per-task``
 * You can request multiple GPUs with syntax like this (in this case for two
   GPUs): ``--gpus-per-node=2``

  .. note:: 

    You may see some scripts use a command line ``--gres=gpu:2`` to specify two
    GPUS. This way of specifying the number of GPUs to use is in the process of
    being deprecated.

Running a GPU job on Slurm involves specifying the required resources and
submitting the job to the scheduler.
Here are the basic steps to run a GPU job on Slurm:

 1. Request the required resources.
    In order to run a GPU job on Slurm, you need to specify the number of GPUs
    and the amount of memory required.
    For example, to request a single GPU with 16GB of memory, you would add the
    following line to your Slurm job script:

    .. code-block:: bash

       #SBATCH --gpus-per-node=2
       #SBATCH --mem=16GB

 2. Load the necessary modules.
    Depending on the software and libraries you are using you may need to load
    additional modules to access the GPU resources. 
    This can usually be done using the module load command. 
    For example, to load the CUDA toolkit:

    .. code-block:: bash

       lua
       module load cuda

 3. Write the job script.
    Create a job script that specifies the commands and arguments needed to run
    your GPU job.
    This can include running a CUDA program, a TensorFlow script, or any other
    GPU-accelerated code.

 4. Submit the job.
    Use the sbatch command to submit the job script to the Slurm scheduler.
    For example:

    .. code-block:: bash

       sbatch my_gpu_job.sh

    Once your job is submitted, Slurm will allocate the requested resources and
    schedule the job to run on a node with the appropriate GPU.
    You can monitor the status of your job using the squeue command and view
    the output using the sacct command once the job completes.

Here's an example script that will return the information on the GPU available
on ``aoraki_gpu``:

.. code-block:: bash

    #!/bin/bash
    #SBATCH --account=account_name
    #SBATCH --partition=aoraki_gpu
    #SBATCH --gpus-per-node=1
    #SBATCH --mem=4GB
    #SBATCH --time=00:00:30
    nvidia-smi

For a slightly more involved example consider the following C code.

.. code-block:: c

  #include<stdio.h>

  #define BLOCKS 2
  #define WIDTH 16

  __global__ void whereami() {
    printf("I'm thread %d in block %d\n", threadIdx.x, blockIdx.x);
  }

  int main() {
    whereami<<<BLOCKS, WIDTH>>>();
    cudaDeviceSynchronize();
    return 0;
  }

If this is stored in the file ``whereami.cu`` and compiled with 
``nvcc whereami.cu -o whereami`` we can use the Slurm job script

.. code-block:: bash

    #!/bin/bash
    #SBATCH --account=account_name
    #SBATCH --partition=aoraki_gpu
    #SBATCH --gpus-per-node=1
    #SBATCH --mem=4GB
    #SBATCH --time=00:00:30
    whereami

to obtain output such as the following (ordering of lines may differ):

.. code-block::

  I'm thread 0 in block 1 
  I'm thread 1 in block 1 
  I'm thread 2 in block 1 
  I'm thread 3 in block 1 
  I'm thread 4 in block 1 
  I'm thread 5 in block 1 
  I'm thread 6 in block 1 
  I'm thread 7 in block 1 
  I'm thread 8 in block 1 
  I'm thread 9 in block 1 
  I'm thread 10 in block 1 
  I'm thread 11 in block 1 
  I'm thread 12 in block 1 
  I'm thread 13 in block 1 
  I'm thread 14 in block 1 
  I'm thread 15 in block 1 
  I'm thread 0 in block 0 
  I'm thread 1 in block 0 
  I'm thread 2 in block 0 
  I'm thread 3 in block 0 
  I'm thread 4 in block 0 
  I'm thread 5 in block 0 
  I'm thread 6 in block 0 
  I'm thread 7 in block 0 
  I'm thread 8 in block 0 
  I'm thread 9 in block 0 
  I'm thread 10 in block 0 
  I'm thread 11 in block 0 
  I'm thread 12 in block 0 
  I'm thread 13 in block 0 
  I'm thread 14 in block 0 
  I'm thread 15 in block 0 




