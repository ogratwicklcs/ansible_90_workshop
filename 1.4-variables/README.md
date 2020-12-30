# Workshop Exercise - Using Variables

## Table of Contents

* [Objective](#objective)
* [Guide](#guide)
* [Intro to Variables](#intro-to-variables)
* [Step 1 - Create Variable Files](#step-1---create-variable-files)
* [Step 2 - Create index.html Files](#step-2---create-indexhtml-files)
* [Step 3 - Create the Playbook](#step-3---create-the-playbook)
* [Step 4 - Test the Result](#step-4---test-the-result)
* [Step 5 - Ansible Facts](#step-5---ansible-facts)

# Objective

Ansible supports variables to store values that can be used in Playbooks. Variables can be defined in a variety of places and have a clear precedence. Ansible substitutes the variable with its value when a task is executed.

This exercise covers variables, specifically
- How to use variable delimiters `{{` and `}}`
- What `host_vars` and `group_vars` are and when to use them
- How to use `ansible_facts`

# Guide

## Intro to Variables

Variables are referenced in Playbooks by placing the variable name in double curly braces:

<!-- {% raw %} -->
```yaml
Here comes a variable {{ variable1 }}
```
<!-- {% endraw %} -->

The recommended practice to provide variables in the inventory is to define them in files located in two directories named `host_vars` and `group_vars`:

  - To define variables for a group "servers", a YAML file named `group_vars/servers.yml` with the variable definitions is created.

  - To define variables specifically for a host `node1`, the file `host_vars/node1.yml` with the variable definitions is created.

> **Tip**
>
> Host variables take precedence over group variables.


## Step 1 - Create Variable Files

In this lab you will change the `index.html` on your web servers to show the development environment (dev/prod) a server is deployed in.

On the ansible control host, as the `student<X>` user, create the directories to hold the variable definitions in `~/ansible-files/`:

```bash
[student<X>@ansible ansible-files]$ mkdir host_vars group_vars
```

Now create two files containing variable definitions. We’ll define a variable named `stage` which will point to different environments, `dev` or `prod`:

  - Create the file `~/ansible-files/group_vars/web.yml` with this content:

```yaml
---
stage: dev
```

  - Create the file `~/ansible-files/host_vars/node2.yml` with this content:

```yaml
---
stage: prod
```

What is this about?

  - For all servers in the `web` group the variable `stage` with value `dev` is defined. So as default we flag them as members of the dev environment.

  - For server `node2` this is overriden and the host is flagged as a production server.

## Step 2 - Create web.html Files

Now create two files in `~/ansible-files/files/`:

One called `prod_web.html` with the following content:

```html
<body>
<h1>This is a production webserver, take care!</h1>
</body>
```

And the other called `dev_web.html` with the following content:

```html
<body>
<h1>This is a development webserver, have fun!</h1>
</body>
```

## Step 3 - Create the Playbook

Now you need a Playbook that copies the prod or dev `web.html` file - according to the "stage" variable.

Create a new Playbook called `deploy_index_html.yml` in the `~/ansible-files/` directory.

> **Tip**
>
> Note how the variable "stage" is used in the name of the file to copy.

<!-- {% raw %} -->
```yaml
---
- name: Copy web.html
  hosts: web
  become: yes
  tasks:
  - name: copy web.html
    copy:
      src: "{{ stage }}_web.html"
      dest: /var/www/html/index.html
```
<!-- {% endraw %} -->

  - Run the Playbook:

```bash
[student<X>@ansible ansible-files]$ ansible-playbook deploy_index_html.yml
```

## Step 4 - Test the Result

The Playbook should copy different files as index.html to the hosts, use `curl` to test it. Check the inventory again if you forgot the IP addresses of your nodes.

```bash
[student<X>@ansible ansible-files]$ curl node1
<body>
<h1>This is a development webserver, have fun!</h1>
</body>
[student1@ansible ansible-files]$ curl node2
<body>
<h1>This is a production webserver, take care!</h1>
</body>
[student1@ansible ansible-files]$ curl node3
<body>
<h1>This is a development webserver, have fun!</h1>
</body>
```

## Step 5 - Ansible Facts

Ansible facts are variables that are automatically discovered by Ansible from a managed host. Remember the "Gathering Facts" task listed in the output of each `ansible-playbook` execution? At that moment the facts are gathered for each managed nodes. Facts can also be pulled by the `setup` module. They contain useful information stored into variables that administrators can reuse.

To get an idea what facts Ansible collects by default, on your control node as your student user run:

```bash
[student<X>@ansible ansible-files]$ ansible node1 -m setup
```

----
**Navigation**
<br>
[Previous Exercise](../1.3-playbook) - [Next Exercise](../1.5-surveys)


