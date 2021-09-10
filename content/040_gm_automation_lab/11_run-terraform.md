---
title: "Automate your tests"
chapter: true
weight: 11
---

# Automate your tests

In this section, we will automate everything by running a script from the start of the devices, the execution of the tests and the stop of the devices.

## Setup

### Create the script file

Create a new file named `script.sh` in your `~/environment` folder, and copy and paste the following code into it:

<script src="https://gist.github.com/lilinor/47ab238e304344bdcb0e5a7778ba5c55.js"></script>

Save the script and make it executable with `chmod +x script.sh`

### Get the Android Sample project

In `~/environment`, simply run this command `git clone https://github.com/thomascarpentier/MyAndroidApp.git`. It will fetch an Android Sample project with Espresso that we will be able to run.

## Description of the steps in the script file

Here is a detailed description of what our script does.

### 1. Start of the devices

To start the devices, the script runs 2 commands:

```
terraform init
terraform apply -auto-approve
```

### 2. Wait for the devices to be fully booted

The script gets the number of devices started dynamically with:

```
count=$(terraform output --json | jq -r '.instance_id.value | length')
```

It then runs the commands below to be sure that the devices are fully booted before running any tests:

```
for i in $(seq 0 $(($count-1)))
do
    id=$(terraform output --json | jq ".instance_id.value[$i]" | tr -d \")
    echo "wait for instance $id is ready"
    aws ec2 wait instance-running --instance-ids $id
    aws ec2 wait instance-status-ok --instance-ids $id
done
```

### 3. Connect to the devices

ADB isn't enabled on the device so we need to enable it first. If you don't want to enable adb everytime, you can create a custom image with [adb enabled](https://docs.genymotion.com/paas/latest/04_ADB.html) either from API or user interface.

The script uses our HTTP API to do this:

```
curl -X POST "https://[instance_ip]/api/v1/configuration/adb" -H  "accept: application/json" -H  "Content-Type: application/json" -u  "CREDENTIALS" -d "{\"active\":true,\"active_on_reboot\":true}"
```

where CREDENTIALS is the value of `username:password`. By default, **username** is **genymotion** and **password** is the **instance id**.

To connect to the device: `adb connect $instance_ip:5555` where instance_ip is the public ip of the device.

The code in the script looks like this :

```
for i in $(seq 0 $(($count-1)))
do
    id=$(terraform output --json | jq ".instance_id.value[$i]" | tr -d \")
    ip=$(terraform output --json | jq ".public_ip.value[$i]" | tr -d \")
    credentials=$(echo -n 'genymotion:$id' | openssl base64)

    curl -k -X POST "https://$ip/api/v1/configuration/adb" -H  "accept: application/json" -H  "Content-Type: application/json" -u "genymotion:$id" -d "{\"active\":true,\"active_on_reboot\":true}" 

    echo "connect $ip"
    adb connect $ip:5555

done
```

### 4. Build the Android project

The workspace is now connected to the devices with adb, we can therefore run the tests.
We need to go to the `MyAndroidApp` project, build and run the Espresso tests

```
cd MyAndroidApp/
.gradlew cAT
```

### 5. Destroy the devices

When the tests are finished, the devices needs to be shutdown, otherwise you still get be billed. We need first to go back to the home directory then call the terraform command to destroy all the resources we created for this workshop.

```
cd ../
terraform destroy 
```

## Run the script

Run `./script.sh` to run the script.
