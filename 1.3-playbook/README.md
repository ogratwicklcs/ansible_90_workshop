# Workshop Exercise - Writing Your First Playbook

## Table of Contents

- [Objective](#objective)
- [Guide](#guide)
  - [Step 1 - Playbook Basics](#step-1---playbook-basics)
  - [Step 2 - Creating a Directory Structure and File for your Playbook](#step-2---creating-a-directory-structure-and-file-for-your-playbook)
  - [Step 3 - Running the Playbook](#step-3---running-the-playbook)
  - [Step 4 - Extend your Playbook: Start &amp; Enable Apache](#step-4---extend-your-playbook-start--enable-apache)
  - [Step 5 - Extend your Playbook: Create an web.html](#step-5---extend-your-playbook-create-an-indexhtml)


# Objective

This exercise covers using Ansible to build two Apache web servers on Red Hat Enterprise Linux. This exercise covers the following Ansible fundamentals:

- Understanding Ansible Module parameters
- Understanding and using the following modules
  - [yum module](https://docs.ansible.com/ansible/latest/modules/yum_module.html)
  - [service module](https://docs.ansible.com/ansible/latest/modules/service_module.html)
  - [copy module](https://docs.ansible.com/ansible/latest/modules/copy_module.html)
- Understanding [Idempotence](https://en.wikipedia.org/wiki/Idempotence) and how Ansible Modules can be idempotent  

# Guide

While Ansible ad hoc commands are useful for simple operations, they are not suited for complex configuration management or orchestration scenarios. For such use cases *playbooks* are the way to go.

Playbooks are files which describe the desired configurations or steps to implement on managed hosts. Playbooks can change lengthy, complex administrative tasks into easily repeatable routines with predictable and successful outcomes.

A playbook is where you can take some of those ad-hoc commands you just ran and put them into a repeatable set of *plays* and *tasks*.

A playbook can have multiple plays and a play can have one or multiple tasks.  The goal of a *play* is to map a group of hosts.  The goal of a *task* is to implement modules against those hosts.

> **Tip**
>
> Here is a nice analogy: When Ansible modules are the tools in your workshop, the inventory is the materials and the Playbooks are the instructions.

## Step 1 - Playbook Basics

Playbooks are text files written in YAML format and therefore need:

  - to start with three dashes (`---`)

  - proper indentation using spaces and **not** tabs\!

There are some important concepts:

  - **hosts**: the managed hosts to perform the tasks on

  - **tasks**: the operations to be performed by invoking Ansible modules and passing them the necessary options.

  - **become**: privilege escalation in Playbooks, same as using `-b` in the ad hoc command.

> **Warning**
>
> The ordering of the contents within a Playbook is important, because Ansible executes plays and tasks in the order they are presented.

> **Tip**

## Step 2 - Creating a Directory Structure and File for your Playbook

It’s time to create your first Ansible Playbook. In this lab you create a playbook to set up an Apache web server in three steps:

  1. Install httpd package

  2. Enable/start httpd service

  3. Copy over an web.html file to each web host

This Playbook makes sure the package containing the Apache web server is installed on `node1`.

On your control host **ansible**, create a directory called `ansible-files` in your home directory and change directories into it. 

```bash
[student<X>@ansible ~]$ mkdir ansible-files
[student<X>@ansible ~]$ cd ansible-files/
```

In the new directory add a file called `apache.yml` with the below content.  In order to create a new file in VS Code go to `File` > `New File` this will create an empty unsaved file.  To save the file either use your keyboard shortcut `Ctrl` + `S` or go to `File` > `Save` and in the dropdown make sure to save the file in the new directory you created.

```yaml
---
- name: Apache server installed
  hosts: node1
  become: yes
```

This shows one of Ansible’s strengths: The Playbook syntax is easy to read and understand. In this Playbook:

  - A name is given for the play via `name:`.

  - The host to run the playbook against is defined via `hosts:`.

  - We enable user privilege escalation with `become:`.

> **Tip**
>
> You obviously need to use privilege escalation to install a package or run any other task that requires root permissions. This is done in the Playbook by `become: yes`.

Now that we've defined the play, let's add a task to get something done. We will add a task in which yum will ensure that the Apache package is installed in the latest version. Modify the file so that it looks like the following listing:

```yaml
---
- name: Apache server installed
  hosts: node1
  become: yes
  tasks:
  - name: latest Apache version installed
    yum:
      name: httpd
      state: latest
```
> **Tip**
>
> Since playbooks are written in YAML, alignment of the lines and keywords is crucial. Make sure to vertically align the *t* in `task` with the *b* in `become`. 

In the added lines:

  - We started the tasks part with the keyword `tasks:`.

  - A task is named and the module for the task is referenced. Here it uses the `yum` module.

  - Parameters for the module are added:

    - `name:` to identify the package name
    - `state:` to define the wanted state of the package


## Step 3 - Running the Playbook

Ansible Playbooks are executed using the `ansible-playbook` command on the control node. Before you run a new Playbook it’s a good idea to check for syntax errors:

```bash
[student<X>@ansible ansible-files]$ ansible-playbook --syntax-check apache.yml
```

Now you should be ready to run your playbook:

```
[student<X>@ansible ansible-files]$ ansible-playbook apache.yml
```

The output should not report any errors but provide an overview of the tasks executed and a play recap summarizing what has been done. 
## Step 4 - Extend your Playbook: Start & Enable Apache

The next part of the Ansible Playbook makes sure the Apache application is enabled and started on `node1`.

On the control host, as your student user, edit the file `~/ansible-files/apache.yml` to add a second task using the `service` module. The Playbook should now look like this:

```yaml
---
- name: Apache server installed
  hosts: node1
  become: yes
  tasks:
  - name: latest Apache version installed
    yum:
      name: httpd
      state: latest
  - name: Apache enabled and running
    service:
      name: httpd
      enabled: true
      state: started
```

With the second task we make sure the Apache server is indeed running on the target machine. Run your extended Playbook:

```bash
[student<X>@ansible ansible-files]$ ansible-playbook apache.yml
```

Note the output now: Some tasks are shown as "ok" in green and one is shown as "changed" in yellow.

  - Run the Playbook a second time to get used to the change in the output and note the color change for the second task.

## Step 5 - Extend your Playbook: Create an web.html

So why not use Ansible to deploy a simple `web.html` file? On the ansible control host, as the `student<X>` user, create the directory `files` to hold file resources in `~/ansible-files/`:

```bash
[student<X>@ansible ansible-files]$ mkdir files
```

Then create the file `~/ansible-files/files/web.html` on the control node:

```html
<body>
<h1>Apache is running fine</h1>
</body>
```


On the control node as your student user edit the file `~/ansible-files/apache.yml` and add a new task utilizing the `copy` module. Additionally, we might want to scale the automation. So instead of running the playbook only on `node1` let us change the `hosts: node1` to `hosts: web`. 
It should now look like this:

```yaml
---
- name: Apache server installed
  hosts: web
  become: yes
  tasks:
  - name: latest Apache version installed
    yum:
      name: httpd
      state: latest
  - name: Apache enabled and running
    service:
      name: httpd
      enabled: true
      state: started
  - name: copy web.html
    copy:
      src: web.html
      dest: /var/www/html/index.html
```

The new task uses the `copy` module and defines the source and destination options for the copy operation as parameters.

Run your extended Playbook:

```bash
[student<X>@ansible ansible-files]$ ansible-playbook apache.yml
```


```bash
[student<X>@ansible ansible-files]$ ansible localhost -m uri -a "url=http://node1"
```

----
**Navigation**
<br>
[Previous Exercise](../1.2-adhoc) - [Next Exercise](../1.4-variables)
