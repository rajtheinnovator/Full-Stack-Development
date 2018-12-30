## Fetch the Source Code and VM Configuration
### Windows: Use the Git Bash program (installed with Git) to get a Unix-style terminal.

### Other systems: Use your favorite terminal program.

#### *Fork the starter repo*
Log into your personal Github account, and fork the [fullstack-nanodegree-vm](https://github.com/udacity/fullstack-nanodegree-vm) so that you have a personal repo you can push to for backup. Later, you'll be able to use this repo for submitting your projects for review as well.

#### *Clone the remote to your local machine*
From the terminal, run the following command (be sure to replace <username> with your GitHub username): [git clone http://github.com/<username>/fullstack-nanodegree-vm fullstack]()

This will give you a directory named `fullstack` that is a clone of your remote `fullstack-nanodegree-vm` repository.

#### *Run the virtual machine!*
Using the terminal, change directory using the command `cd fullstack/vagrant`, then type `vagrant up` to launch your virtual machine.

Once it is up and running, type `vagrant ssh`. This will log your terminal into the virtual machine, and you'll get a Linux shell prompt. When you want to log out, type exit at the shell prompt. To turn the virtual machine off (without deleting anything), type `vagrant halt`. If you do this, you'll need to run vagrant up again before you can log into it.

Now that you have Vagrant up and running type `vagrant ssh` to log into your virtual machine (VM). Change directory to the `/vagrant` directory by typing `cd /vagrant`. This will take you to the shared folder between your virtual machine and host machine.

Sharing files between the vagrant virtual machine and your home machine.
Be sure to change to the /vagrant directory by typing `cd /vagrant` before creating new files or pasting files that you want to be shared between your host machine and the VM.