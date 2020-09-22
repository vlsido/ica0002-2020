# Lab 3

Goal of this lab is to deploy a simple web application with Ansible.

The application itself is rather trivial but all the stack needed to get it
running may be challenging to set up.

We recommend approaching the problems one by one, and moving in smaller steps.
This is the fastest way to complete these tasks. 


## The application

We couldn't find any good web application for you to practice on -- which is
not too easy nor too difficult to set up, so we've created out own :)

Meet the
[AGAMA: A (very) Generic App to Manage Anything](https://github.com/hudolejev/agama).

Goal of this task is to get this application running on server, in the simplest
possible but still automated production-grade way.


## Requirements

There is one additional requirements for this lab:

**If you are using `pip` to install Python packages make sure to install them to
Virtualenv (not system-wide).**

In any case package instllation via APT -- if possible -- is a recommended
approach.


## Task 1: preparations

Update your Ansible inventory file from the [lab 2](../02-web-server/lab.md) and
add the `app_servers` group. It should contain the single host -- your VM
[from this list](http://193.40.156.86/vms.html).

Please do not remove any groups from the inventory file that you have added
before; just add a new group.

Once done, your inventory file should look like similar to this:

	vm-1  ansible_host=... ansible_user=... ansible_python_interpreter=...

	[app_servers]
	vm-1

	[web_servers]
	vm-1

Create a playbook named `lab03_web_app.yaml` with a single play named `Web app`.

This playbook should apply a role named `agama` (that you will create later) on
every app server, i. e. should contain something like this:

	hosts: app_servers
	roles:
      - agama

Tasks in this role would require privilege escalation. If you don't rememeber
how to configure it check out lab 2 task 1.


## Task 2: the application

Create an Ansible role named `agama` that deploys the AGAMA application on the
managed host. If you don't remember the exact file structure in the role
subdirectory check out the Ansible related slides from the
[lab 2](../02-web-server).

This role should:
 1. Create a system user named `agama`
 2. Create the directory `/opt/agama` owned by user `agama` where the
    application will be installed
 3. Install application dependencies
 4. Install the application itself

For the last two steps make sure to check out
[AGAMA installation instructions](https://github.com/hudolejev/agama#installation).

Once done, you can verify that the application is working by running this
command on the **managed host** as user `ubuntu`:

	sudo -u agama AGAMA_DATABASE_URI=sqlite:////opt/agama/test.sqlite3 python3 /opt/agama/agama.py

This is a manual step just to verify the result. Don't do it with Ansible
please.

You should see this output, without any errors:

	 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)

Note that it's **not** the proper way to run web applications, but is good
enough for this lab to test your solution at this stage.

Hints:
 - You already know how to use Ansible modules `apt` and `user`. So use them :)
 - Use Ansible module
   [file](https://docs.ansible.com/ansible/latest/modules/file_module.html)
   to create a directory.
 - Use Ansible module
   [get_url](https://docs.ansible.com/ansible/latest/modules/get_url_module.html)
   to download remote publicly available files from the Internet to the managed
   host.


## Task 3: uWSGI

Create an Ansible role named `uwsgi` the deploys
[uWSGI](https://uwsgi-docs.readthedocs.io) on the managed host. uWSGI is an
application container server that will run our application.

This role should:
 1. Install uWSGI packages; Ubuntu 18.04 packages that we'll need are named
    `uwsgi` and `uwsgi-plugin-python`.
 2. Add the uWSGI configuration for AGAMA so that the application is run by user
    `agama`
 4. Ensure that uWSGI service is started (unconditionally) and enabled to start
    automatically on system boot.

Requirements:
 - AGAMA application should be run by user `agama`.
 - uWSGI should listen on local interface (not 0.0.0.0 or any other non-local
   IP). Or, you can use UNIX socket file if you want.
 - uWSGI system service (named `uwsgi`) should be restarted automatically if
   uWSGI configuration was changed.
 - uWSGI system service shoud **not** be restarted if uWSGI configuration was
   not changed.
 - AGAMA should be configured to use SQLite3 database located at
   `/opt/agama/db.slite3`. No need to _create_ that file explicitly though,
   AGAMA will create it automatically on the first request.

Add this role to your playbook, and run Ansible to set up uWSGI.

Once done, you can verify that uWSGI is started by running this command on a
managed host (port will be different if you changed it):

	netstat -lnpt | grep 5000

You should see the output very similar to this (last column may differ):

	tcp  0  0  127.0.0.1:5000  0.0.0.0:*  LISTEN  9001/uwsgi

This is an indication that uWSGI is set up correctly.

If you feel that something is not working as expected -- fix it first before
approaching the next task.

Hints:
 - Check out
   [AGAMA deployment instructions](https://github.com/hudolejev/agama#running)
   for the uWSGI configuration file example
 - Add uWSGI configuration file for AGAMA to
   `/etc/agama/apps-enabled/agama.ini`. uWSGI on Debian/Ubuntu is pre-configured
   to pick configuration files automatically from this directory. Note that this
   is Debian/Ubuntu specific behavior brought to you by `uwsgi` package from the
   APT repository; default (upstream) uWSGI is configured differently.
 - uWSGI configuration reference can be found
   [here](https://uwsgi-docs.readthedocs.io/en/latest/Options.html); this may be
   useful if you feel that AGAMA configuration example is not enough.
 - uWSGI logs can be found in `/var/log/uwsgi/app` directory. If something is
   not working as expected you will probably find an answer there.
 - You can additionaly run `service uwsgi status` on the managed host to check
   the uWSGI system service status, and view a few latest logged messages. It is
   definitely helpful while debugging.
 - Use Ansible module
   [service](https://docs.ansible.com/ansible/latest/modules/service_module.html)
   to manage system services (start, restart etc.).
 - Use
   [Ansible handlers](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html#handlers-running-operations-on-change)
   to handle service restarts correctly.


## Task 4: Nginx

Update the `nginx` role from the previous lab so that Nginx is configured as a
frontend for uWSGI.

Requirements:
 - Nginx should listen on a public interface port 80 -- otherwise your public
   URL just won't work :(
 - Nginx system service (named `nginx`) should be restarted automatically if
   Nginx site (uWSGI) configuration was changed.
 - Nginx system service shoud **not** be restarted if Nginx configuration was
   not changed.

Add this role to your playbook, and run Ansible to set up everything.

Once done, AGAMA should be available at
[your public URL](http://193.40.156.86/vms.html).

Feel free to click around and break all the things. If you feel that AGAMA app
has some issues please consider
[reporting them](https://github.com/hudolejev/agama#contributing).

If you need to 'reset' the app just delete the `db.slite3` file; AGAMA will
re-create it (if missing) on the next request.

Hints:
 - You can find Nginx configuration examples in the lecture slides and in the
   AGAMA deployment instructions (section 'Running').
 - Add Nginx configuration file for uWSGI to `/et/nginx/sites-enbled/default`.
   This file comes from the APT package -- you can safely overwrite it for our
   labs.
 - Nginx uWSGI module configuration reference can be found
   [here](http://nginx.org/en/docs/http/ngx_http_uwsgi_module.html#uwsgi_pass).
 - Nginx logs can be found in `/var/log/nginx` directory.


## Expected result

Your repository contains these files and directories; other files may (and
should) be there but these are the ones that we'll check:

```
ansible.cfg
hosts
lab03_web_app.yaml
roles/agama/tasks/main.yaml
roles/nginx/tasks/main.yaml
roles/uwsgi/tasks/main.yaml
```

Your repository also contains all the required files from the previous labs.

AGAMA with uWSGI and Nginx can be installed and configured on empty machine by
running exactly this command exactly once:

	ansible-playbook lab03_web_app.yaml

Running the same command again does not make any changes on the managed host.

AGAMA web application is available on
[your public URL](http://193.40.156.86/vms.html).
