# Guides for HKUST HPC3 Cluster
HPC3 service a computing cluster is powered by ITSC, which provide a shared cluster that allow research groups to do complicated computing tasks. It is the latest version of HKUST HPC series, and provides cpu and gpu nodes to give a better working efficiency. HPC3 is short for High-Performance Computing, the detailed introduction of HPC3 can be found from:

https://itsc.hkust.edu.hk/services/academic-teaching-support/high-performance-computing/hpc3-cluster

## 1. Submit an application form on the ITSC platform
The application form can be found from the link below:

https://hkust.service-now.com/itsc?id=sc_cat_item&sys_id=f299c0dedbb1d01058a29c18db961992

### 1.1 Login with ITSC Account
User name: your_account (e.g. your_account@connect.ust.hk)


Password: password for your account

### 1.2 Fill in the Form
![alt text](image.png)
### 1.3 Submit and Wait:
After submitting your application form, you need to wait for nearly one month for the application to be processed. 


## 2. Setup Linux System (For Windows System)
The HPC3 Cluster is built based on Linux System, so you should have at least one way to access Linux system. Two common ways are discussed in this part.

### 2.1 Virtual Machine
#### 2.1.1. Download Virtual Machine
Many kinds of virtual machine are suitable for running HPC3, like VMware, Oracle VM VirtualBox, etc. You can download any of them and follow the steps provided by official websites to setup your virtual machine.

#### 2.1.2. Download Ubuntu System
Ubuntu operating system of many versions are available from the official website: https://ubuntu.com, you can download a suitable one and save it to your computer for future use.

#### 2.1.3. Setup and Initialization System
Setup a suitable number of memory and cores allocated to your virtual machine and write it with the downloaded Ubuntu disk. Initialize your own Ubuntu System and you can finally use it properly.

### 2.2 Windows Subsystem for Linux (WSL)
This method is easier than using virtual machine for only PowerShell operations are needed to setup and launch the Linux System. To install a WSL on your own computer, you can refer to the following website for details:

https://learn.microsoft.com/en-us/windows/wsl/install


or follow the steps below to set up your WSL system.

#### 2.2.1 Download and Installation
Press `Win + R`, enter `powershell` and press `Ctrl + Shift + Enter` to run as administrator. 

Then enter `wsl --install -d ubuntu` to download and install. 

After finishing the process, you are required to input your username and password. 

You can use the same username as ITSC account for convenience. 

If you see the line starting with a green `Username@LocalName`, it means you have successfully downloaded and installed the WSL and Ubuntu System.


## 3. Guides for HPC3
### 3.1 Logging in
First, make sure you are using the network 'eduroam'  in HKUST which is the only network to provide HPC3 service. Then, launch your Ubuntu system and enter `ssh your_account@hpc3.ust.hk` and your password, select a way to verify your account. If the following welcome page is shown, you log in HPC3 successfully.

### 3.2 Files Transfer
#### 3.2.1 Download Certain Application
You can use SFTP (SSH File Transfer Protocol) client such as FileZilla on PC Windows to do the file transfer. The detailed download and installation process are not discussed here. Any other apps capable in file transfer are welcome.

#### 3.2.2 Launch and Connect
Launch your `FileZilla Client` and connect your HPC3 cluster with following content:

Enter `your_account@hpc3.ust.hk` in Host, `your_account` in Username and your password in Password respectively, and press `Quick Connect`.

Because you are required to verify your account through DUO Service every time you log in, so it may come up with some errors calling for `interactive` login option. Therefore, we should set it at: `File-Site Manager...-General` and change the `Logon type` to `interactive`, then you will successfully connect to your HPC3 cluster and transfer files after you pass the DUO verification process.

#### 3.2.3 Files Transfer
After your successful connection, you can transfer files by simlpy dragging or double-click on the certain files.

### 3.3 Submit Jobs on HPC3
The HPC3 Service use Simple Linux Utility for Resource Management (SLURM) to manage jobs, therefore some special rules should be followed to submit and run the jobs.
#### 3.3.1 Anaconda installation
To manage your environments and packages on your HPC3 platform, you are suggested by ITSC official to use Anaconda. 


Download the corresponding Anaconda on HPC3 platform, locate your target apt from Anaconda official website, for example, use `wget https://repo.anaconda.com/archive/Anaconda3-2024.02-1-Linux-x86_64.sh` to download.


Then, use the command `ls` to get the folder information, and use command `bash your_Anaconda_apt` to install Anaconda.


After all the settings are done, restart the HPC3 platform, and the Anaconda is ready for future use.

#### 3.3.2 Set up Environments
To run your python files properly, you should set up your environment and install packages in need ahead of time. Some useful commands are listed below for reference:


`conda -n create your_env` -- create environment


`conda activate your_env` -- activate your environment


`conda deactivate your_env` -- deactivate your environment


`conda install package_name` -- general command for installing packages


`conda list` --show all installed packages in your certain environment


`conda info --env` --show all the created environments (including base)

Some other useful commands can be refered to official website.

#### 3.3.3 Write and Run .sh File
To submit and run jobs properly, you should wirte a script first to tell the system what to do, the detailed guidance is listed in the websites:


https://itsc.hkust.edu.hk/services/academic-teaching-support/high-performance-computing/hpc3-cluster/jobs


Include your command like `python -m example.examples` in the script so that it can be executed directly.


Finally, transfer the .sh file to HPC3 platform, and run it by command `sbatch your_sript.sh`.


A template of script is shown as follow:


```
#!/bin/bash

# NOTE: Lines starting with "#SBATCH" are valid SLURM commands or statements,
#       while those starting with "#" and "##SBATCH" are comments.  Uncomment
#       "##SBATCH" line means to remove one # and start with #SBATCH to be a
#       SLURM command or statement.


#SBATCH -J slurm_job #Slurm job name

# Set the maximum runtime, uncomment if you need it
##SBATCH -t 48:00:00 #Maximum runtime of 48 hours

# Enable email notificaitons when job begins and ends, uncomment if you need it
##SBATCH --mail-user=user_name@ust.hk #Update your email address
##SBATCH --mail-type=begin
##SBATCH --mail-type=end

# Choose partition (queue) "cpu" or "cpu-share" or replace with partition you can access
#SBATCH -p cpu-share
##SBATCH -p gpu-share (use gpu-share to run the jobs)

# Use 2 nodes and 80 cores
#SBATCH -N 2 -n 80

# Setup runtime environment if necessary
# For example, setup intel MPI environment
module swap gnu8 intel

# Go to the job submission directory and run your application
cd $HOME/apps/slurm
mpirun ./your_mpi_application

python -m examples.dogbone.example
```

#### 3.3.4 Check Jobs' Status and Queue
There are some special commands for SLURM system, some common commands are listed as follows:

`sinfo` -- show 


`squeue` -- show all users' status of jobs in the overall HPC3 system


`squeue -u username` -- show the status of jobs of certain user, PD refers to pending, R refers to running, SG refers to completing


`sbatch` -- run a certain bash file


Also, keep in mind that, the resources allocated to each PI group are limited, every group can at most use 6 cpu-share nodes, 2 gpu-share nodes and 2 himen-share nodes at the same time, and for each job, it is set globally that, the walltime is 1 day (24 hours), any jobs exceed 24 hours will be killed no matter it is finished or not, so please be very careful about it. In addition, there is priority problems when there are too many jobs running at the same time, so there is probability that your job is suspended and others' jobs are running advanced.

## 4. Reference
For further information, you can refer to the ITSC websites:

https://itsc.hkust.edu.hk/services/academic-teaching-support/high-performance-computing/hpc3-cluster
