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
* **NOTE:** If you don't have access to the cluster, skip to Step Z. Then, follow the same process and choose the appropriate install script for your system.

1. Log into the Great Lakes cluster.
```
ssh UNIQNAME@greatlakes.arc-ts.umich.edu
```

1. You should be in your home folder but let's check to make sure.
```
pwd
```

1. To install Conda, we first need to find the proper installer on the [website](https://docs.conda.io/en/latest/miniconda.html). We will be installing Miniconda instead of Anaconda since it only contains the essentials and tends to run faster.

1. Right-click and copy the link address for the Miniconda Linux 64-bit installer that uses Python 3.7.
* **NOTE:** If using a different operating system, make sure to choose the correct installer.

![Image of Miniconda Linux installers](/images/miniconda_installer.png)

1. Return to your terminal and use `wget` to download the installation script to your home folder on the cluster.
```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

1. Verify the installation script is now in your home folder.
```
ls
```

1. It's good practice to make sure downloaded files weren't corrupted during the download process. One way to do that is using **hashes** which are unique signatures generated based on the file contents. Some (but not all) sites will make the hash available so you can verify the download was successful. Let's check ours now.

```
shasum -a 256 Miniconda3-latest-Linux-x86_64.sh
```

1. Compare the hash to the hash listed next to the installer. If it doesn't match (ours does so no problems), try downloading again or check your internet settings.



## Using Conda

## Additional Tips














