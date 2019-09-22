# Conda on the Cloud

William Close  
September 23, 2019


## Summary

* 

## Introduction to Conda

### What is Conda?

### Why is Conda useful?



## Installing Conda

The process for installing Conda is the same across most operating systems but we will use the University of Michigan Great Lakes HPC cluster as a uniform Linux environment for the purposes of this tutorial.
> **NOTE:** If you don't have access to the cluster, skip to Step 3. Then, make sure to choose the correct installer and adjust accordingly going forward.

<br />

1. Log into the Great Lakes cluster.

```
ssh UNIQNAME@greatlakes.arc-ts.umich.edu
```

<br />
  
2. You should be in your home folder but let's check to make sure.

```
pwd
```  

<br />

3. To install Conda, we first need to find the proper installer on the [website](https://docs.conda.io/en/latest/miniconda.html). We will be installing Miniconda instead of Anaconda since it only contains the essentials and tends to run faster. Right-click and copy the link address for the Miniconda Linux 64-bit installer that uses Python 3.7.

![Image of Miniconda Linux installers](/images/miniconda_installer.png)

<br />

4. Return to your terminal and use `wget` to download the installation script to your home folder on the cluster.

```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

<br />

5. Verify the installation script is now in your home folder.
```
ls
```

<br />

6. It's good practice to make sure downloaded files weren't corrupted during the download process. One way to do that is using **hashes** which are unique signatures generated based on the file contents. Some (but not all) sites will make the hash available so you can verify the download was successful. Let's check ours now.

```
shasum -a 256 Miniconda3-latest-Linux-x86_64.sh
# 8a324adcc9eaf1c09e22a992bb6234d91a94146840ee6b11c114ecadafc68121  Miniconda3-latest-Linux-x86_64.sh

```

<br />

7. Compare the hash to the hash listed next to the installer. If it doesn't match (ours does so no problems), try downloading again or check your internet settings.

<br />

8. To install Miniconda, we need to run the script we just downloaded.

```
bash Miniconda3-latest-Linux-x86_64.sh
```

<br />

9. Follow the prompts on the screen to install the program to your home folder. When it asks whether you want to initialize Miniconda3, say `yes`. The process should only take a few minutes.

<br />

10. When initializing, the installer will add a code chunk to the bottom of your `~/.bashrc` file. This file is used for customizing your terminal and environment. When the chunk is added to this file, it sets up your environment for using Conda though it does so based on which shell you are currently using. This is normally a good thing but can cause problems when submitting jobs to the cluster because the cluster's shell configuration is slightly different than the one we interact with. If we don't alter the code at the bottom of the `~/.bashrc`, our environment won't be passed to the cluster nodes properly and our jobs will fail. To fix this, we need to comment out most of the lines.

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

11. Now that we've finished setting everything up, we need to reload the shell session for our changes to take place. When we source `~/.bash_profile`, it should also source `~/.bashrc`.

```
source ~/.bash_profile
```

<br />

12. Lastly, we'll check to make sure we can access the `conda` command.

```
conda --help
```

<br />

## Using Conda

## Additional Tips














