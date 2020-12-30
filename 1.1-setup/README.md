# Workshop Exercise - Check the Prerequisites

## Table of Contents

* [Objective](#objective)
* [Guide](#guide)
* [Your Lab Environment](#your-lab-environment)
* [Step 1 - Access the Environment](#step-1---access-the-environment)
* [Step 2 - Working the Labs](#step-2---working-the-labs)

# Objective

- Understand the lab topology and how to access the environment.
- Understand how to work the workshop exercises


# Guide

## Your Lab Environment

In this lab you work in a pre-configured lab environment. You will have access to the following hosts:

| Role                 | Inventory name |
| ---------------------| ---------------|
| Ansible Control Host | ansible        |
| Managed Host 1       | node1          |
| Managed Host 2       | node2          |
| Managed Host 3       | node3          |

## Step 1 - Access the Environment

Login to your control host via SSH:

> **Warning**
>
> Replace **11.22.33.44** by your **IP** provided to you, and the **X** in student**X** by the student number provided to you.

    ssh studentX@11.22.33.44

> **Tip**
>
> The password will be located on with the rest of the connection information for your personal lab

Then become root:

    [student<X>@ansible ~]$ sudo -i

Most prerequisite tasks have already been done for you:

  - Ansible software is installed

  - SSH connection and keys are configured

  - `sudo` has been configured on the managed hosts to run commands that require root privileges.

Check Ansible has been installed correctly

    [root@ansible ~]# ansible --version
    ansible 2.9.3
    [...]

> **Note**
>
> Ansible is keeping configuration management simple. Ansible requires no database or running daemons and can run easily on a laptop. On the managed hosts it needs no running agent.

Log out of the root account again:

    [root@ansible ~]# exit
    logout

> **Note**
>
> In all subsequent exercises you should work as the student\<X\> user on the control node if not explicitly told differently.

## Step 2 - Working the Labs

You might have guessed by now this lab is pretty commandline-centric…​ :-)

  - Don’t type everything manually, use copy & paste from the browser when appropriate. But stop to think and understand.

> **Tip**
>
> In the lab guide commands you are supposed to run are shown with or without the expected output, whatever makes more sense in the context.


----
**Navigation**
<br>
[Next Exercise](../1.2-adhoc)

[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md#section-1---ansible-engine-exercises)
