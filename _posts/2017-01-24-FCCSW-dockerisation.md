---
title: FCC and Docker
subtitle: Virtualisation vs Dockerisation
category: fcc
img: ../images/../images/docker.png
more: https://github.com/jlingema/fcc-tutorials/blob/master/FccVirtualMachine.md
related: dockerisation
---

The predominant way of distributing software in HEP is through distributed file-systems such as CVMFS and AFS which is
mounted in the scientific linux (SL) environment.

In the context of the FCC not all users have access to SL clusters. Our first approach was the natural choice of a
vanilla virtual machine, which was a nightmare to maintain. We took a step back and had a look at
[CernVM](https://cernvm.cern.ch/) which is very light-weight and can use CVMFS out of the box. Problem solved.

Then we were confronted with users from Latin America who have little to no internet connection. We took a look at Docker
and how to deploy our software in a Docker container. This works quite well, although, due to our dependencies the images
become relatively large (1-2 Gb). But it's a neat solution!
