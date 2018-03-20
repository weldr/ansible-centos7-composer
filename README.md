Ansible playbook for lorax-composer on a CentOS7 VM
===================================================

This playbook is meant to be used on a minimal CentOS7 installation. You need
to have ansible installed on your local system, and you need to know the IP
address of the system you want to run lorax-composer on.

The VM should have at least 4G of RAM, 2 cores and more than 10G of disk space
(images can be quite large). More is better.

After you have installed the base system you should run:

    ansible-playbook --ssh-extra-args "-o CheckHostIP=no -o StrictHostKeyChecking=no" -k -i <ip-of-the-vm>, install-composer.yml

If you are using ssh-key access to the VM you don't need the `-k`, just make sure
to ssh-add the key to your local ssh-agent first.

This will install cockpit, welder-web, and lorax-composer. Cockpit should be
available on port 9090 of the system. Note that the current version of
welder-web (0.0.1-1) does not support composing an image (the UI for that is a mockup).

After ansible is finished you can experiment with lorax-composer directly from
the VM's cmdline. ssh to the system, or use the console provided by
cockpit.

welder-web is available at https://<ip-of-the-vm>:9090 and can be used to view
and edit recipes. You will need to accept the self-signed SSL certificate in
your browser and login as root with the password you set while installing the
VM.

List Recipes
------------

    curl --unix-socket /run/weldr/api.socket http:///api/v0/recipes/list

Download the Recipe (as JSON)
-----------------------------

    curl --unix-socket /run/weldr/api.socket http:///api/v0/recipes/info/http-server

Compose a root.tar.xz of the http-server Recipe
-----------------------------------------------

    curl --unix-socket /run/weldr/api.socket -X POST -H "Content-Type: application/json" -d '{"recipe_name": "http-server", "compose_type": "tar", "branch": "master"}' http:///api/v0/compose

This will return some JSON with the UUID of the build, use this to monitor and download the results.

You can monitor the status of the build with:

    curl --unix-socket /run/weldr/api.socket http:///api/v0/compose/status/<uuid>

Or view the end of the anaconda.log with:

    curl --unix-socket /run/weldr/api.socket http:///api/v0/compose/log/<uuid>

More documentation of the API routes can be found here:

https://github.com/rhinstaller/lorax/blob/lorax-composer/src/pylorax/api/v0.py

The output can be downloaded via the API, or you can look directly at the results in:

    /var/lib/lorax/composer/results/<uuid>/
