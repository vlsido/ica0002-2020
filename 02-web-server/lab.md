# Lab 2

## Task 1

Update your Ansible inventory file from the [lab 1](../01-intro/lab.md) and add
the `web_servers` group. It should contain the single host -- your VM
[from this list](http://193.40.156.86/vms.html). Example:

	vm-1  ansible_host=... ansible_user=... ansible_python_interpreter=...

	[web_servers]
	vm-1

Create a playbook file named `lab02_web_server.yaml` with a single play named
`Web server`. This will set up a web server on your managed host.

This play should run on every hosts from `web_servers` group:

	hosts: web_servers

Note: Ansible logs in to your managed host as unprivileged user `ubuntu` but
most of the following tasks require privilege escalation (should be run as
`root`). Easiest way to achieve that is to set the `become` property in the
play:

	become: yes

See also: https://docs.ansible.com/ansible/latest/user_guide/become.html

Please just add new files; do not delete any files from previous labs.


## Task 2

Create an Ansible role named `users` that adds two more Linux users on your
managed host and enables SSH user key based authentication for them:
 - `juri` (GitHub username: `hudolejev`)
 - `roman` (GitHub username: `romankuchin`)

Update the `lab02_web_server.yaml` playbook to apply this role to all your web
servers.


## Task 3

Create an Ansible role named `nginx` that installs and configures Nginx web
server with a custom index page.

Use [this](./index.html) index page, replace `VM_NAME` with your VM name.

Update the `lab02_web_server.yaml` playbook to apply this role to all your web
servers.

Requirements:
 - Web page mentioned above should be accessible on
   [public URL assigned to you](http://193.40.156.86/vms.html)

Do not change the port Nginx is listening on. By default it's 80, and custom
port in your public URL is already set up to forward to port 80 on your managed
host.


## Task 4

List the **full paths** of the files that were added, deleted or changed on
server during this playbook run, in the file named `lab02_changed_files.txt`.
Just the list of files and what happened to each of them, for example:

	/path/to/foo.txt added
	/path/to/bar/baz.txt changed
	/path/to/xyzzy.tar.gz deleted


## Expected result

Your repository contains these files:

 - `ansible.cfg`
 - `hosts`
 - `roles/nginx/files/index.html`
 - `roles/nginx/tasks/main.yaml`
 - `roles/users/tasks/main.yaml`
 - `lab02_changed_files.txt`
 - `lab02_web_server.yaml`

Nginx is installed and configured on empty machine by running exactly this
command exactly once:

	ansible-playbook lab02_webserver.yaml

Users `juri` and `roman` can SSH to that machine using their SSH keys.

Running the same command again does not make any changes on managed host.

Custom web page that you have uploaded on managed host is served on
[your public URL](http://193.40.156.86/vms.html).


## Hints

These Ansible modules will probably be helpful:
 - [apt](https://docs.ansible.com/ansible/latest/modules/apt_module.html)
 - [authorized_key](https://docs.ansible.com/ansible/latest/modules/authorized_key_module.html)
 - [copy](https://docs.ansible.com/ansible/latest/modules/copy_module.html)
 - [user](https://docs.ansible.com/ansible/latest/modules/user_module.html)

Use standard OS package repositories to install packages:
https://packages.ubuntu.com/

Check the Nginx configs in `/etc/nginx/sites-enabled` to find the default HTML
directory location (server root).
