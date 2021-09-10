---
title: "Scale Android tests with Genymotion and Terraform"
chapter: true
weight: 5
---

# Scale Android tests with Genymotion and Terraform
When doing Continuous Integration, there are different components :

<img src=/images/gm-lab/ci-workflow.png>

The Continuous Integration Server has a central role : it will fetch the source code of an application from a source code platform (e.g Github), spawn the virtual devices, build the application and run the tests. Once finished, it will destroy the virtual devices.

In this lab, the cloud9 workspace will act as the Continuous Integration Server. We will focus on how to automate the spawn of virtual devices, run of Android tests and finally the stop of the virtual devices.

We won't go into details regarding the testing frameworks, you can refer to the resources section for that. We provide a working Android sample application from where you will be able to automate tests using Espresso framework. Once you understand how to scale Genymotion Android virtual devices up and down and run tests on them, you will be able to use any Android testing framework of your choice.
