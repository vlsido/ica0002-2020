# Lab 1
## Git Intro
You will keep all the code (and more) written during this course in a Git
repository.

Teachers will check your work in your Git repo -- not in your text editor.
Having all the task completed with results pushed to your Git repository is a
requirement to access the exam.

GitHub is a well-known and widely used (but not the only one) service to host
Git repositories. We'll use GitHub for this course as a collaboration platform.

If you feel some lack of experience with Git and GitHub we recommend to read
[this tutorial](https://guides.github.com/introduction/git-handbook). It's ok to use GUI version:[GitHub Desktop](https://desktop.github.com/)
## Ansible Intro
We will use Ansible in this course as a configuration management tool. There are
dozens of configuration management tools out there but we've chosen Ansible as
one of the simplest one to get started with.

You can run Ansible from your laptop directly in case you have Linux or MacOS. In case you have Windows, we recommend to use [VirtualBox](https://www.virtualbox.org/wiki/Downloads) running Ubuntu as guest VM.
## Task 1: Set up your Git repository
If you have a GitHub account already, skip this step. Otherwise
[create a new GitHub account](https://github.com/join). The most basic free
account is more than enough -- you won't need any premium GitHub features for
this course.
[Create a GitHub repository](https://github.com/new) named `ica0002`. Note: this
repository should be **private**!

Add our course bot as a collaborator to your repository. This is needed for the
teachers to provision virtual machines for you to practice, and check your task
submissions. Go to your new repository settings, select `Manage access` in the
left menu, click `Invite collaborator` and add user `ica0002-bot` (here is its
GitHub profile: [https://github.com/ica0002-bot](https://github.com/ica0002-bot).
Once you have completed all the steps above your repository should appear in
[this list](http://193.40.156.86/students.html) automatically within 2..3
minutes. If it does not, please ask the teachers for help.

Note: You don't have to wait until your repository is added to this list, you
can continue with the next task.
## Task 2: Set up SSH keys
If you have an SSH  keypair already you can reuse it for this course. You can
check for existing SSH keys on your machine by running

    ls -la ~/.ssh

If there are files named `id_rsa` and `id_rsa.pub` you're probably good to go
already. If not, generate a new keypair by running `ssh-keygen`.
Your **public** SSH key can be fount in `~/.ssh/id_rsa.pub` file. Add this key
(entire file content) to your GitHub account:
 - In GitHub web UI click your profile icon in the top right corner
 - Select `Settings`
 - Select `SSH and GPG keys` in the left menu
 - Click `New SSH key`
 - Paste the content of `~/.ssh/id_rsa.pub` file to the `Key` field
 - click `Add SSH key`
Once you have added your public key to your GitHub account our bot should detect
it automatically within 2..3 minutes. You can see the result
[here](http://193.40.156.86/students.html).
Note: You don't have to wait until your key is added to this list, you can
continue with the next task.
## Task 3: Install Ansible
Note: we will use Ansible version 2.9 for this course.
Teachers will have this version installed and use it to check your tasks. Your code is considered
working only if it executes successfully on Ansible 2.9. If you're using other
Ansible versions -- you're on your own with them.

Note for Windows users: Ansible does not officially support running on Windows,
neither do we support Windows as a workstation. Although we have nothing against
you get Ansible running on Windows, we recommend to install Ubuntu (Desktop
or Server) in VirtualBox, and run the Ansible from there -- it's simpler.

For Linux or OS X, we recommend to use Python virtual env:

    python3 -m venv ~/ansible-venv
    ~/ansible-venv/bin/pip install ansible==2.9
    ~/ansible-venv/bin/ansible --version

Last command should print something like

    ansible 2.9.0

Then, add this line to your `~/.profile` file so you can use 'shorter' commands:

    PATH="$HOME/ansible-venv/bin:$PATH"

Logout and log in again. Now this command should also work:

    ansible --version
## Task 4: Access your virtual machine
First, make sure that your Git repository is set up correctly -- check
[this list](http://193.40.156.86/students.html) for details.
Then, run this command using correct port number from [this list](http://193.40.156.86/vms.html):

    ssh -p122 ubuntu@193.40.156.86

You should be able to access the virtual machine with your SSH key. If not,
please ask the teachers for help.
## Task 5: Create Ansible playbook
Note: example of first playbook you can find [here](01-demo).

Step 1: Create your inventory file (use 01-demo/hosts as example). Use [VM list](http://193.40.156.86/vms.html)
to fill this file with your connection parameters.

Step 2: Create *ansible.cfg*. You can just copy the contents from 01-demo/ansible.cfg.

Step 3: Create Ansible role. You can just copy the contents from 01-demo/roles/test_connection/main.yaml.

Note: Folder structure and names matters. Same as name of file: *main.yaml*

Step 4: Create Ansible playbook. You can just copy the contents from 01-demo/test_ansible.yaml.

Step 5: Run Ansible playbook: `ansible-playbook test_ansible.yaml`. You should see only "ok" messages.
