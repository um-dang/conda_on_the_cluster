# Conda on the Cloud

**William Close**<br />
**September 23, 2019**


## Summary

* 

## Introduction to Conda

### What is Conda?

### Why is Conda useful?



## Installing Conda

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

**11.** Now that we've finished setting everything up, we need to reload the shell session for our changes to take place. When we source `~/.bash_profile`, it should also source `~/.bashrc`.

```
source ~/.bash_profile
```

<br />

**12.** Lastly, we'll check to make sure we can access the `conda` command.

```
conda --help
```

<br />

## Creating and Managing Environments

Now that we've installed Conda, we're ready to start how to use it as an environment-based package manager. Environments are an integral part of Conda-based workflows. They are customizable, reproducible, and shareable modules that contain the resources for a specific task or set of tasks. Environments also help avoid [**"dependency hell"**](https://en.wikipedia.org/wiki/Dependency_hell) where required programs are incompatible with previously installed programs or program versions.

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

**3.** You should now see that the word `base` is in your prompt showing that we've loaded the base environment. Another way to check which environment you have active is to look at the `$CONDA_PREFIX` variable. If you don't have any environment loaded, the output will be blank.

```
echo $CONDA_PREFIX
```

<br />

**4.** Now that we have the `base` environment loaded, let's see what programs it contains for us to use.

```
conda list
```
The output from this command lists all of the programs, their versions, the build number, and the channel they came from (if outside of the default channels, more on that later).

<br />

**5.** We can check to make sure we are using the programs from our environment by using `which` to print the executable path or by checking our `$PATH` variable. The `$PATH` variable lists the order of folders from first to last that the shell will look through to find executables. The shell will execute the first binary it finds and ignore the rest so it's important our `$PATH` is in the correct order.

```
which python
echo $PATH
```

<br />

**6.** In a typical workflow, it's good practice to create environments for each task (script) being executed to keep the environment running as fast as possible, reduce the likelihood of conflicting programs/versions, and assist in debugging when things don't work out. Let's create a new environment for analyzing 16S bacterial sequencing data with [**mothur**](https://mothur.org/wiki/Main_Page). First, let's deactivate the current environment.
> **NOTE:** Deactivating the current environment is being done here for the sake of clarity. The process of creating and managing a new environment are exactly the same whether another environment is active or not.

```
conda deactivate
```

<br />

**7.** Next, let's create an empty environment for our `mothur` analysis.

```
conda create -n mothur
```

<br />

**8.** We should see it in our list of available environments now. Also note that the new environment is created in a subdirectory of the `miniconda3` folder in our home directory. This is where binaries, etc. will be downloaded to if you ever need to find them.

```
conda env list
```

<br />

**9.** We can activate the new environment.

```
conda activate mothur
```

<br />

**10.** If we look at which programs the environment contains, we'll see that it's empty.

```
conda list
```

<br />

**11.** To add new programs (in this case `mothur`), we can download them from the [**Anaconda Cloud**](https://anaconda.org/). Anaconda Cloud is a series of repositories called **channels** that contain version controlled programs for use with Conda. Different channels will have different themes and users are also able to create their own private channels. For example, the main bioinformatics channel is called [**Bioconda**](https://bioconda.github.io/index.html).

<br />

By searching on Anaconda Cloud, we can find which versions of `mothur` are available and how to download it. We can see that `mothur` is available from multiple channels but we'll use Bioconda specifically.

![Image showing mothur available from multiple channels](/images/anaconda_mothur_search_results.png)

<br />

If we click on the Bioconda version, we'll be taken to the information page for `mothur` where we can see details about versions, metadata, and most importantly for us, installation instructions.

![Image of bioconda/mothur package page](/images/anaconda_mothur_package_page.png)

<br />

**12.** Before we install mothur, we need to make two quick changes to our default channels. Similar to our `~/.bashrc`, we can also create a `~/.condarc` for [configuring Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/use-condarc.html). First, when you install Conda, there are a few default channels but one that is also useful to add is [**Conda-Forge**](https://conda-forge.org/). Conda-Forge packages are typically more up-to-date than the default channels and, thus, it helps satisfy potential dependency issues. To create the `~/.condarc` if it doesn't already exist and add Conda-Forge to your channels, type the following.

```
conda config --add channels conda-forge
```

Second, for more complex reasons, we should also change how priority is set when searching for packages. You can read more about it [here](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-channels.html#strict-channel-priority).

```
conda config --set channel_priority strict
```

<br />

**13.** Now, go back to the website, copy the installation code for `mothur`, paste it into your terminal, and run the installation. By specifying `-c` during the install, we can tell Conda which channel to use. This is particularly important when downloading packages that have multiple copies on multiple channels. Also, notice that Conda automatically determines and installs the dependencies required for installing/running the program. This is one of the major advantages of using a package manager like Conda.

```
conda install -c bioconda mothur
```

<br />

**14.** When the installation finishes, see what other programs/dependencies were installed.

```
conda list
```

<br />

**15.** Let's try running `mothur` to see if it worked.

```
which mothur
mothur
quit()
```
**Success!**

<br />

**16.** The last important function is the ability to export environments for sharing. To do so, we'll use `conda`'s export function while the desired environment is active.

```
conda env export > environment.yaml
```

<br />

**17.** If you take a look at the new environment file, you can see that it lists all of the channels, packages, and package versions from the current environment. The `prefex` line also encodes the path of the environment. 

```
cat environment.yaml
```

<br />

**18.** These types of `.yaml` files can be used to create new environments as part of workflows. While we programmatically generated a `.yaml` environment file, you can also generate them manually. I've included an example as part of this repo. We can create an environment from that file using the following.
```
conda env create -f example.yaml
```

## Conclusions

Summary
Tips


## Additional Resources














