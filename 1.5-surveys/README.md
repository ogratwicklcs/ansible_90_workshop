# Exercise - Surveys

## Table Contents

* [Objective](#objective)
* [Guide](#guide)
* [The Apache-configuration Role](#the-apache-configuration-role)
* [Create a Template with a Survey](#create-a-template-with-a-survey)
   * [Create Template](#create-template)
   * [Add the Survey](#add-the-survey)
* [Launch the Template](#launch-the-template)
* [What About Some Practice?](#what-about-some-practice)

# Objective

Demonstrate the use of Ansible Tower [survey feature](https://docs.ansible.com/ansible-tower/latest/html/userguide/job_templates.html#surveys). Surveys set extra variables for the playbook similar to ‘Prompt for Extra Variables’ does, but in a user-friendly question and answer way. Surveys also allow for validation of user input.

# Guide

You have installed Apache on all hosts in the job you just run. Now we’re going to extend on this:

- Use a proper role which has a Jinja2 template to deploy an `index.html` file.

- Create a job **Template** with a survey to collect the values for the `index.html` template.

- Launch the job **Template**

Additionally, the role will also make sure that the Apache configuration is properly set up - in case it got mixed up during the other exercises.


## The Apache-configuration Role

The Playbook and the role with the Jinja template already exist in the Github repository **https://github.com/ansible/workshop-examples** in the directory `rhel/apache`**`.

The role also deploys a static configuration for Apache. This is to make sure that all changes done in the previous chapters are overwritten and your examples work properly.

Because the Playbook and role is located in the same Github repo as the `apache_install.yml` Playbook you don't have to configure a new project for this exercise.

## Create a Template with a Survey

Now you create a new Template that includes a survey.

### Create Template

- Go to **Templates**, click the ![plus](images/green_plus.png) button and choose **Job Template**

- **NAME:** Create index.html

- Configure the template to:

    - Use the `Workshop Project` **Project**

    - Use the `apache_role_install.yml` **Playbook**

    - To run on `web` group

    - To run in privileged mode

Try for yourself, the solution is below.

> **Warning**
>
> **Solution Below\!**

<table>
  <tr>
    <th>Parameter</th>
    <th>Value</th>
  </tr>
  <tr>
    <td>NAME</td>
    <td>Create index.html</td>
  </tr>
  <tr>
    <td>JOB TYPE</td>
    <td>Run</td>
  </tr>
  <tr>
    <td>INVENTORY</td>
    <td>Workshop Inventory</td>
  </tr>
  <tr>
    <td>Project</td>
    <td>Workshop Project</td>
  </tr>  
  <tr>
    <td>PLAYBOOK</td>
    <td><code>rhel/apache/apache_role_install.yml</code></td>
  </tr>
  <tr>
    <td>CREDENTIAL</td>
    <td>Workshop Credential</td>
  </tr>
  <tr>
    <td>LIMIT</td>
    <td>web</td>
  </tr> 
  <tr>
    <td>OPTIONS</td>
    <td>Enable Privilege Escalation</td>
  </tr>          
</table>

- Click **SAVE**

> **Warning**
>
> **Do not run the template yet!**

### Add the Survey

- In the Template, click the **ADD SURVEY** button

- Under **ADD SURVEY PROMPT** fill in:

<table>
  <tr>
    <th>Parameter</th>
    <th>Value</th>
  </tr>
  <tr>
    <td>PROMPT</td>
    <td>First Line</td>
  </tr>
  <tr>
    <td>ANSWER VARIABLE NAME</td>
    <td><code>first_line</code></td>
  </tr>
  <tr>
    <td>ANSWER TYPE</td>
    <td>Text</td>
  </tr>         
</table>

- Click **+ADD**

- In the same way add a second **Survey Prompt**

<table>
  <tr>
    <th>Parameter</th>
    <th>Value</th>
  </tr>
  <tr>
    <td>PROMPT</td>
    <td>Second Line</td>
  </tr>
  <tr>
    <td>ANSWER VARIABLE NAME</td>
    <td><code>second_line</code></td>
  </tr>
  <tr>
    <td>ANSWER TYPE</td>
    <td>Text</td>
  </tr>         
</table>

- Click **+ADD**

- Click **SAVE** for the Survey

- Click **SAVE** for the Template

## Launch the Template

Now launch **Create index.html** job template.

Before the actual launch the survey will ask for **First Line** and **Second Line**. Fill in some text and click **Next**. The next window shows the values, if all is good run the Job by clicking **Launch**.

> **Tip**
>
> Note how the two survey lines are shown to the left of the Job view as **Extra Variables**.

After the job has completed, check the Apache homepage. In the SSH console on the control host, execute `curl` against the IP address of your `node1`:

```bash
$ curl node1
<body>
<h1>Apache is running fine</h1>
<h1>This is survey field "First Line": line one</h1>
<h1>This is survey field "Second Line": line two</h1>
</body>
```
Note how the two variables where used by the playbook to create the content of the `index.html` file.

----
**Navigation**
<br>
[Previous Exercise](../1.4-variables) - [Next Exercise](../1.6-system-roles)


