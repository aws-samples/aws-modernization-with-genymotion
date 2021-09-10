---
title: "4. Setup the Android environnement"
chapter: true
weight: 15
---

# Setup the Android environment

To be able to build an Android project and run automated tests, the Android SDK needs to be installed and configured on the workspace

## 1. Download the Command Line Tools

From the [download page](https://developer.android.com/studio#command-tools), get the download link for the Linux package by clicking on the package link.
<img src=/images/selfguided/android-sdk-download.png>
It will open a pop-up and by click-right on Download Android Command Line to copy the link
<img src=/images/selfguided/android-sdk-download-link.png>

Open a terminal on your workspace, go to your home directory (running `cd` command will do the trick) and download it with
 `wget <your_link>`

 At the time of the writing the latest sdk download link is `https://dl.google.com/android/repository/commandlinetools-linux-7583922_latest.zip`

## 2. Setup the Android Tools (CLI)

We will create the folder where the Android SDK will be installed and will be your $ANDROID_HOME. On the same workspace terminal :
Create a folder `AndroidSdk`

```bash
 ~ $ mkdir AndroidSdk
 ~ $ cd AndroidSdk
```

Then, unzip the file here. At the time of the writing, the zip file is `commandlinetools-linux-7583922_latest.zip`. Please replace it with the name of the file you downloaded if it has changed:

```bash
unzip ~/commandlinetools-linux-7583922_latest.zip -d ./
```

You can then remove the zip file:

```bash
rm ~/commandlinetools-linux-7583922_latest.zip
```

Finally, move all the content of the directory **cmdline-tools** to a created **tools** directory:

```bash
~/AndroidSdk $ cd cmdline-tools
~/AndroidSdk/cmdline-tools $ mkdir tools
~/AndroidSdk/cmdline-tools $ mv -i * tools
```

{{% notice tip %}}
If you get an error after running the `mv` command ("mv: cannot move ‘tools’ to a subdirectory of itself, ‘tools/tools’"), you can ignore it.
{{% /notice %}}

## 3. Add the tools to the $PATH environment variable & set the $ANDROID_HOME environment variable

Adding the tools to the $PATH environment variable will let you call the tools from any location on your terminal. 
Setting the $ANDROID_HOME environment variable is a prerequisite to run any Android automated tests.

Let's add those in the your **bash_profile**. You can use nano for that

Run `nano ~/.bash_profile`. Add those following lines in the file :

```bash
export ANDROID_HOME=$HOME/AndroidSdk
export PATH=$ANDROID_HOME/cmdline-tools/tools/bin/:$PATH
export PATH=$ANDROID_HOME/platform-tools/:$PATH
```

Once finished, save it and `source ~/.bash_profile`. 

Run `sdkmanager` and hit enter. You should see a progress bar.

## 4. Installing the Android SDK

This is the final step and your Android environment will be all setup!

For this workshop, we will use an Android project that requires **android-29** package - so, we will use this package version.

We will install the minimum required packages which are `platform-tools` and `platforms;android-29`:

```bash
sdkmanager "platform-tools" "platforms;android-29"
```

Type `y` to accept the user conditions.

Your Android environment is now setup!

{{% notice tip %}}
You can use the command `sdkmanager --list` to list all the available sdks, platform-tools, etc.
{{% /notice %}}
