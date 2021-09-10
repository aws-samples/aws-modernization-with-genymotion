---
title: "3. Create a Workspace"
chapter: true
weight: 14
---

# Set up the Workspace

[AWS Cloud9](https://aws.amazon.com/cloud9/) is a cloud-based integrated development environment (IDE) that lets you write, run, and debug your code with just a browser. It includes a code editor, debugger, and terminal. Cloud9 comes prepackaged with essential tools for popular programming languages, including JavaScript, Python, PHP, and more, so you don’t need to install files or configure your laptop for this workshop.

We will use Amazon Cloud9 to access our AWS accounts via the AWS CLI in this Workshop.  There are a few steps to complete to set this up:

1. [Create a new Cloud9 IDE environment](#create-a-new-cloud9-ide-environment)
2. [Configure Cloud9 IDE environment](#configure-cloud9-ide-environment)

## Create a new Cloud9 IDE environment

1. Within the AWS console, use the region drop list to select **us-east-1 (N. Virginia)**.  This will ensure the workshop script provisions the resources in this same region..

2. Navigate to the [Cloud9 console](https://console.aws.amazon.com/cloud9/home) or just search for it under the **AWS console services** menu.

3. Click the **Create environment** button

4. For the name use `genymotion-workshop`, then click **Next step**

    <img src=/images/setup/c9create.png>

5. In the **Instance type** box, select **Other Instance type** and choose the instance type **t3.medium**

6. Leave all the other settings as default and click **Next step** followed by **Create environment**

{{% notice info %}}
This will take about 1-2 minutes to provision
{{% /notice %}}

## Configure Cloud9 IDE environment

When the environment comes up, customize the environment by:

1. Close the **welcome page** tab

1. Close the **lower work area** tab

1. Open a new tab in the main work area and select **New Terminal** (or press Ctrl+T)

{{% notice tip %}}
If you don't like this dark theme, you can change it from the **View / Themes** Cloud9 workspace menu.
{{% /notice %}}

{{% notice tip %}}
Cloud9 requires third-party-cookies. You can whitelist the [specific domains](https://docs.aws.amazon.com/cloud9/latest/user-guide/troubleshooting.html#troubleshooting-env-loading).  You are having issues with this, Ad blockers, javascript disablers, and tracking blockers should be disabled for the cloud9 domain, or connecting to the workspace might be impacted.
{{% /notice %}}

## Add more storage to your Cloud9 IDE environment

Setting the whole Android testing environment will require space. To prevent out of storage issues, we will set the storage to 20 GB.
Create a `resize.sh` file in your environment folder (`~/environment`), copy and paste the content of our template below:

<script src="https://gist.github.com/lilinor/bc4647a73ab56014daa680f23b06f83f.js"></script>

1. Get the public IP address of your workspace from the ec2 admin panel or with this command : `dig +short myip.opendns.com @resolver1.opendns.com` and replace `your_instance_public_ip` in `INSTANCEID=$(curl http://your_public_ip/latest/meta-data/instance-id)` with your instance public IP.

2. Make the script executable with `chmod +x resize.sh`

3. Run the script to resize to 20 GB: `./resize.sh 20`

4. Reboot your cloud9 instance with `sudo reboot`
