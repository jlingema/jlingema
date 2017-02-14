---
title: Deploying with Docker
excerpt: Getting HEP applications to run in a docker container.
categories: code other
layout: post
permalink: dockerisation
---

### Premise

The predominant way of distributing software in HEP is through distributed file-systems such as CVMFS and AFS which is
mounted in the scientific linux (SL) environment.

In the context of the FCC not all users have access to SL clusters. Our first approach was the natural choice of a
vanilla virtual machine, which was a nightmare to maintain. We took a step back and had a look at
[CernVM](https://cernvm.cern.ch/) which is very light-weight and can use CVMFS out of the box. Problem solved?

We were confronted with users from Latin America who have little to no internet connection. We took a look at Docker
and how to deploy our software in a Docker container.

### Software Packaging

We have recently revisited how we package our software. Here, we are following the guidelines of the HEP software
foundation who had a look at common solutions. Since we need cross-platform builds of interdependent software packages,
[Spack](https://github.com/LLNL/spack) is a neat solution.

In Spack packages are described in Python classes that implement functions like `build` or `configure`, which allow to
specify how the corresponding build step should be performed. Software versions are hashed to avoid misconfiguration.
FCC specific packages are described in a [dedicated repository](https://github.com/jlingema/fcc-spack).

The stack is described in YAML configuration files, where specific versions of packages can be specified. After compilation
a file-system view of the software is generated (the file-system view is essentially a folder structure like one
encounters in e.g. `/usr`).

### Dockerfile

With a Dockerfile based on ubuntu we are going with the usual approach of layering our Dockerfile, starting with two layers:

1. Intall [ROOT](http://root.cern.ch) with its dependencies, see [this dockerfile](https://github.com/jlingema/fcc-spi/blob/spack/docker/Dockerfile-ubuntu%2Broot).
1. Install our own software stack, see [this dockerfile](https://github.com/jlingema/fcc-spi/blob/spack/docker/Dockerfile-fcc).

The first layer takes about 45 minutes on my MacBook, due to the compilation time of ROOT. The second layer only about 10 minutes.
To install the software we use the aforementioned Spack, except for dependencies that are available through APT (took
reduce build-time). To build against these "system libraries", we define them in a [YAML file](https://github.com/jlingema/fcc-spi/blob/spack/docker/packages-ubuntu16.04.yaml)
that is combined with our normal [stack definition YAML](https://github.com/jlingema/fcc-spi/blob/spack/docpage/_data/packages.yml).

The resulting image is about 1.7 GB big, mostly due to dependencies and the ROOT install:

```
$ docker load -i fccimage.tgz
32d75bc97c41: Loading layer [=====================>] 134.6 MB/134.6 MB
87f743c24123: Loading layer [=====================>] 15.87 kB/15.87 kB
bbe6cef52379: Loading layer [=====================>] 11.78 kB/11.78 kB
3d515508d4eb: Loading layer [=====================>] 4.608 kB/4.608 kB
5972ebe5b524: Loading layer [=====================>] 3.072 kB/3.072 kB
8662db6e2e28: Loading layer [=====================>] 501.4 MB/501.4 MB  <-- dependencies
3183f8dfc6c4: Loading layer [=====================>]  2.56 kB/2.56 kB
d6d3ac7cd225: Loading layer [=====================>] 31.37 MB/31.37 MB
eac251323be3: Loading layer [=====================>]   299 kB/299 kB
8911d5c8447b: Loading layer [=====================>] 5.632 kB/5.632 kB
2e56d49037f1: Loading layer [=====================>] 996.5 MB/996.5 MB  <-- ROOT install
ea5dbec8b476: Loading layer [=====================>] 287.2 kB/287.2 kB
7003400848e3: Loading layer [=====================>] 121.9 kB/121.9 kB
ac09d122a8db: Loading layer [=====================>]   217 MB/217 MB    <-- FCC software
1f264a2c9e7b: Loading layer [=====================>]   343 kB/343 kB
Loaded image: fccimage:0.8
Loaded image: fccimage:latest
```

### Using the image

More information on usage can be found [here](https://github.com/jlingema/fcc-tutorials/blob/master/FccVirtualMachine.md#using-docker).
But in principle it's very easy to spin up an instance with the FCC software through the docker daemon that also
mounts a local directory to keep persistent data:

```
$ docker run -ti --name fccsw -v [local directory]:/work --rm fccimage
+--------------------------------------------------------------------
            FCC  ##        .
           ## ## ##       ==    Welcome to the FCC software docker
        ## ## ## ##      ===    REMINDER: Containers are disposable;
    /""""""""""""""""\___/ ===            Keep your data in volumes!
 ~~{~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~
    \______ o          __/
      \    \        __/         Documentation: fccsw.web.cern.ch
       \____\______/
+--------------------------------------------------------------------
fccuser@220fd963b5d4:~$
```


### Docker vs Virtual Machine

Here is a table summarising when to use a Docker container over the CERN VirtualMachine if you want to use the FCC software:

|                      |               Docker               |                     CernVM                    |
|----------------------|:-----------------------------------|:----------------------------------------------|
| offline work         | Works                              | Need internet connection (CVMFS)              |
| fcc-physics + heppy  | Works                              | Works                                         |
| FCCSW                | Need Gaudi updates                 | Workaround needed (boost)                     |
| Graphics             | X11 (optional)                     | VirtualBox                                    |
| Integration          | ~seamless (mount folders + daemon) | Virtual Box (shared folders + virtualisation) |
| Dev Env              | Use your native env                | Need to setup in VM                           |
