---
title: "The challenge of scaling tests"
chapter: true
draft: false
weight: 12
---

# The Challenge of scaling tests

For day to day testing when a new pull request is triggered, using real devices isn’t scalable and is expensive. Some things are also difficult to test (change device location, battery state). There is a hybrid strategy to adopt and use real devices before a new release for full validation and virtual devices for day to day usage.

That’s how Genymotion Android virtual devices on AWS tackles the challenge of scaling tests. Its instant availability and infinite scalability will let you spawn many virtual devices and scale your tests with ease.

In this workshop, we assume that you are already familiar with testing frameworks as we won’t cover them in detail. We will focus more on how to scale your tests by automating the start/stop of virtual devices and run them by choosing one of the testing frameworks available for mobile automation testing.
