Cluster Software
========

Spack
=====

A large library of software has been installed using the spack package manager. Spack is a package manager for supercomputers, Linux, and macOS. It makes installing scientific software easy. With Spack, you can build a package with multiple versions, configurations, platforms, and compilers, and all of these builds can coexist on the same machine.

Basic usage
--------

The ``spack`` command has many subcommands. You’ll only need a small subset of them for typical usage.

``spack find`` will show the packages that are installed on the cluster. ``spack list`` will show all spack packages that are available. 

``spack info`` will show Information about a particular package. eg. ``spack info r`` will show information about which versions of R are installed, which are available, and all of the depandancies.

``spack install`` will compile and install packages that are not currently on the system. eg. ``spack install mpick`. To install a specific version you can just add an `@` and the version number, eg. `spack install mpich@3.0.4``

``spack uninstall`` works in the same fashion.

Using installed packages
-----------------------

There are several different ways to use Spack packages once you have installed them. As you’ve seen, spack packages are installed into long paths with hashes, and you need a way to get them into your path. The easiest way is to use spack load, which is described in the next section.

Some more advanced ways to use Spack packages include:

    - environments, which you can use to bundle a number of related packages to “activate” all at once, and
    - environment modules, which are commonly used on supercomputing clusters. Spack generates module files for every installation automatically, and you can customize how this is done.

spack load / unload
----------------------
If you have shell support enabled you can use the spack ``load`` command to quickly get a package on your ``PATH``.

For example this will add the ``mpich`` package built with ``gcc`` to your path:

.. code-block:: bash

   $ spack install mpich %gcc@4.4.7

    # ... wait for install ...

    $ spack load mpich %gcc@4.4.7
    $ which mpicc
    ~/spack/opt/linux-debian7-x86_64/gcc@4.4.7/mpich@3.0.4/bin/mpicc 



More Information can be found at:  
https://spack.readthedocs.io/en/latest/basic_usage.html



Conda
==========

On the cluster Miniconda is used for conda environments. To install Miniconda for your user profile use the following commands.


.. code-block:: bash

    mkdir -p ~/miniconda3
    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
    bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
    rm -rf ~/miniconda3/miniconda.sh
    ~/miniconda3/bin/conda init bash


Activating the base environment.

.. code-block:: bash

    source ~/miniconda3/bin/activate


Your command prompt will then change to include "(base) " at the start, in order to remind you that this environment is activated.  You can deactivate the environment by typing:

.. code-block:: bash

    conda deactivate

**Creating and activating a sub-environment**
 |
Although once you have activated the base conda environment, you can in principle start to install packages immediately, your use of conda will generally be better organised if you do not install packages directly into the base environment, but instead use a named sub-environment.  You can have multiple sub-environments under a single base environment, and activate the one that is required at any one time.  Unless you install packages directly into the base environment, your sub-environments will work independently.

To create a named environment (for example, called "myenv"), ensure that the base environment is activated (the command prompt should start with "(base) "), and type:

.. code-block:: bash

    conda create -n myenv

It will show the proposed installation location, and once you answer the prompt to proceed, will do the installation.  If you have followed these instruction, this location should be /home/users/<your_username>/miniconda3/envs/myenv.  You can alternatively give it a different location using the option -p <path> instead of -n <name>.

Once you have created your sub-environment, you can activate it using conda activate <name> for example:

.. code-block:: bash

    conda activate myenv

The command prompt will then change (e.g. to start with "(myenv) ") to reflect this.  Typing conda deactivate once will return you to the base environment; typing it a second time will deactivate conda completely (as above).
 | To List your conda environments type the following:

.. code-block:: bash

    conda env list 

Installing conda packages
Once you have activated a named environment, you can install packages with the conda install command, for example:

.. code-block:: bash

    conda install gcc

You can also force particular versions to be installed.  See the conda cheat sheet for details.

To list the packages installed in the currently activated environment, you can type conda list.

Running packages from your conda environment
In order to run packages from a conda environment that you installed previously, you will first need to activate the environment in the session that you are using.  This means repeating some of the commands typed above.  Of course, you will not need to repeat the steps to create the environment or install the software, but the following may be needed again:

.. code-block:: bash

    source ~/miniconda3/bin/activate

    conda activate myenv

Installing pip packages
Many python packages that are available via PyPI are also available as conda packages in conda-forge, and it is generally best to use these via "conda install" as above.

Nonetheless, you can also install pip packages (as opposed to conda packages) into your conda environment.  However, first you should type:

.. code-block:: bash

    conda install pip

before typing the desired commands such as

.. code-block:: bash

    pip install numpy

If you do not install pip into your sub-environment, then either:

your shell will fail to find the pip executable, or
your shell will find pip in your base environment, which will lead to pip packages being installed into the base environment, resulting in potential interference between your conda environments
Explicitly installing pip into your sub-environment will guard against this.    


Matlab
==========

Matlab GUI can be accessed via the Open Ondemand Applications or Open Ondemand desktop. It is also possible to schedule Matlab jobs via slurm-





 






