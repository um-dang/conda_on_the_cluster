# Conda on the Cloud

**William Close**<br />
**September 23, 2019**


## Guiding Questions

* What is Conda?
* Why is Conda better than other

## Conda in Theory

### What is Conda?

### Why is Conda useful?



## Conda in Practice

### Installing Conda

The process for installing Conda is the same across most operating systems but we will use the University of Michigan Great Lakes HPC cluster as a uniform Linux environment for the purposes of this tutorial.
> **NOTE:** If you don't have access to the cluster, skip to Step 3. Then, make sure to choose the correct installer and adjust accordingly going forward.

<br />

**1.** Log into the Great Lakes cluster.

```
ssh UNIQNAME@greatlakes.arc-ts.umich.edu
```

<br />
  
**2.** You should be in your home folder but let's check to make sure.

```
pwd
```  

<br />

**3.** To install Conda, we first need to find the proper installer on the [**website**](https://docs.conda.io/en/latest/miniconda.html). We will be installing Miniconda instead of Anaconda since it only contains the essentials and tends to run faster. Right-click and copy the link address for the Miniconda Linux 64-bit installer that uses Python 3.7.

![Image of Miniconda Linux installers](/images/miniconda_installer.png)

<br />

**4.** Return to your terminal and use `wget` to download the installation script to your home folder on the cluster.

```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

<br />

**5.** Verify the installation script is now in your home folder.
```
ls
```

<br />

**6.** It's good practice to make sure downloaded files weren't corrupted during the download process. One way to do that is using **hashes** which are unique signatures generated based on the file contents. Some (but not all) sites will make the hash available so you can verify the download was successful. Let's check ours now.

```
shasum -a 256 Miniconda3-latest-Linux-x86_64.sh
# 8a324adcc9eaf1c09e22a992bb6234d91a94146840ee6b11c114ecadafc68121  Miniconda3-latest-Linux-x86_64.sh

```

<br />

**7.** Compare the hash to the hash listed next to the installer. If it doesn't match (ours does so no problems), try downloading again or check your internet settings.

<br />

**8.** To install Miniconda, we need to run the script we just downloaded.

```
bash Miniconda3-latest-Linux-x86_64.sh
```

<br />

**9.** Follow the prompts on the screen to install the program to your home folder. When it asks whether you want to initialize Miniconda3, say `yes`. The process should only take a few minutes.

<br />

**10.** When initializing, the installer will add a code chunk to the bottom of your `~/.bashrc` file. This file is used for customizing your terminal and environment. When the chunk is added to this file, it sets up your environment for using Conda though it does so based on which shell you are currently using. This is normally a good thing but can cause problems when submitting jobs to the cluster because the cluster's shell configuration is slightly different than the one we interact with. If we don't alter the code at the bottom of the `~/.bashrc`, our environment won't be passed to the cluster nodes properly and our jobs will fail. To fix this, we need to comment out most of the lines.

```
nano ~/.bashrc
```
```
# # >>> conda initialize >>>
# # !! Contents within this block are managed by 'conda init' !!
# __conda_setup="$('/home/wlclose/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
# if [ $? -eq 0 ]; then
#     eval "$__conda_setup"
# else
    if [ -f "/home/wlclose/miniconda3/etc/profile.d/conda.sh" ]; then
        . "/home/wlclose/miniconda3/etc/profile.d/conda.sh"
    else
        export PATH="/home/wlclose/miniconda3/bin:$PATH"
    fi
# fi
# unset __conda_setup
# # <<< conda initialize <<<
```

<br />

**11.** Similar to how we use our `~/.bashrc` to configure our shell, we can also create a `~/.condarc` file for [configuring Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html). We can use this to tell Conda where and how to search for packages that we want to download. With Conda, packages are retreived from version-controlled repositories called **channels** and different channels will have different themes. For example, the main bioinformatics channel is called [**Bioconda**](https://bioconda.github.io/index.html).

<br />

Conda comes with a few default channels included but they aren't always that useful. To fix this, we can add new channels to our `~/.condarc` file. Today, we'll be adding the [**Conda-Forge**](https://conda-forge.org/) channel. Packages on Conda-Forge are typically more up-to-date than the default channels and adding it will help satisfy a lot of potential dependency issues.

<br />

To add Conda-Forge to your channels, type the following. If your `~/.condarc` doesn't already exist, this will also create it. 

```
conda config --add channels conda-forge
```

<br />

For the last configuration (and for more complex reasons) we should also change how priority is set when searching for packages. You can read more about it [here](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-channels.html#strict-channel-priority).

```
conda config --set channel_priority strict
```

<br />

**12.** Now that we've finished setting everything up, we need to reload the shell session for our changes to take place. When we source `~/.bash_profile`, it should also source `~/.bashrc`. Alternatively, you can restart your terminal or terminal session.

```
source ~/.bash_profile
```

<br />

**13.** Lastly, we'll check to make sure we can access the `conda` command.

```
conda --help
```

<br />

## Creating and Managing Environments

### Environments 101

Now that we've installed Conda, we're ready to start learning how to use it as an environment-based package manager. Environments are an integral part of Conda-based workflows. They are customizable, reproducible, and shareable modules that contain the resources for a specific task or set of tasks. Environments also help avoid [**"dependency hell"**](https://en.wikipedia.org/wiki/Dependency_hell) where required programs are incompatible with previously installed programs or program versions.

<br />

**1.** To start with, let's see what environments we currently have set up. This will list all of the environments available for us to use along with their locations. 

```
conda env list
```

By default, an environment called `base` is created when installing and intializing Conda. `base` contains a number of standard Python packages that may or may not be useful.

<br />

**2.** Because we altered the code in our `~/.bashrc`, the `base` environment isn't loaded automatically when we log into the shell. This can help speed up tasks if you don't need to use anything in the environment but, if we do need to use something in the environment, we'll need to activate the environment first. We'll start with the `base` environment.

```
conda activate base
```

<br />

**3.** You should now see that the word `base` is in your prompt showing that we've loaded the base environment. Another way to check which environment you have active is to look at the `$CONDA_PREFIX` shell variable. If you don't have any environment loaded, the output will be blank.

```
echo $CONDA_PREFIX
```

<br />

**4.** Now that we have the `base` environment loaded, let's see which programs it contains for us to use.

```
conda list
```
The output from this command lists all of the programs, their versions, the build number, and the channel they came from if outside of the default channels.

<br />

**5.** We can check to make sure we are using the programs from our environment by using `which` to print the executable path or by checking our shell `$PATH` variable. The `$PATH` variable lists the order of folders from first to last that the shell will look through to find executables. The shell will execute the first binary it finds and ignore the rest so it's important our `$PATH` is in the correct order.

```
which python
echo $PATH
```

<br />

### Creating Task-Specific Environments

In a typical workflow, it's good practice to create environments for each task (script) being executed to keep the environment running as fast as possible, reduce the likelihood of conflicting programs/versions, and assist in debugging when things don't work out. Let's create a new environment for analyzing 16S bacterial sequencing data with [**mothur**](https://mothur.org/wiki/Main_Page). 

**1.** First, let's deactivate the current environment.
> **NOTE:** Deactivating the current environment is being done here for the sake of clarity. The process of creating and managing a new environment are exactly the same whether another environment is active or not.

```
conda deactivate
```

<br />

**2.** Next, let's create an empty environment for our `mothur` analysis.

```
conda create -n mothur
```

<br />

**3.** We should see it in our list of available environments now. Also note that the new environment is created in the `miniconda3/envs/` subdirectory in our home folder. This is where binaries, etc. will be downloaded to if you ever need to find them.

```
conda env list
```

<br />

**4.** Let's activate the new environment.

```
conda activate mothur
```

<br />

**5.** If we look at which programs the environment contains, we'll see that it's empty.

```
conda list
```

<br />

**6.** To add new packages (in this case `mothur`), we can search for them on [**Anaconda Cloud**](https://anaconda.org/). Using Anaconda Cloud, we can find which channels we can retrieve `mothur` from, which versions are available, and a lot of other useful information. 

<br />

When we search for `mothur`, we can see that it's available from multiple channels but we'll use Bioconda specifically.

![Image showing mothur available from multiple channels](/images/anaconda_mothur_search_results.png)

<br />

If we click on the Bioconda version, we'll be taken to the information page for `mothur` where we can see details about versions, metadata, and most importantly for us, installation instructions.

![Image of bioconda/mothur package page](/images/anaconda_mothur_package_page.png)

<br />

**7.** Copy the installation code for `mothur`, paste it into your terminal, and run the installation. By specifying `-c` during the install, we can tell Conda which channel to use. This is particularly important when downloading packages that have multiple copies on multiple channels.

```
conda install -c bioconda mothur
```

Even though we only told it to install `mothur`, notice that Conda automatically determines and installs the dependencies required for installing and running it. This is one of the major advantages of using a package manager like Conda as opposed to doing this manually.

<br />

**8.** When the installation finishes, see what other programs/dependencies were installed.

```
conda list
```

<br />

**9.** Next, let's try running `mothur` to see if it worked.

```
which mothur
mothur
```
**Success!** We have now created a new, self-contained environment that contains everything we need to run `mothur` as part of our analysis.

<br />

To exit, type:

```
quit()
```

<br />

### Using `.yaml` Files to Create and Share Reproducible Environments


The last important function we'll go over is the ability to [**create environments from a `.yaml` file**](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-from-an-environment-yml-file). These files can also be shared with others so they can create similar environments. While there are ways to export your current environment (ex: create a file that lists all the packages, versions, etc. in `base` or `mothur`), **it's generally a bad idea** for a number of reasons, the main one being that they aren't typically cross-platform compatible (more information [**here**](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html?highlight=environment#building-identical-conda-environments). We'll create one manually and then show how we use it to create a new environment.

<br />

**1.** To start, create a new `.yaml` file. For the majority of environments, you'll only need to include the three essentials: **i) name**,  **ii) channels**, and **iii) dependencies**. [Setting specific versions](https://docs.conda.io/projects/conda/en/latest/user-guide/concepts/pkg-specs.html#package-match-specifications) to use is optional but highly recommended for reproducibility.

```
nano testEnv.yaml
```
```
name: samtools
channels:
  - conda-forge
  - bioconda
dependencies:
  - python=3.7.3
  - samtools=1.9
```

<br />

**2.** Then, we use that file to create a new environment...

```
conda env create -f testEnv.yaml
```

<br />

**3.** ...which we can now use whenever we want!

```
conda env list
```

## Conclusions

Summary
Tips


## Additional Resources














