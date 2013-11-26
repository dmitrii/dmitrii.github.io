---
layout: post
title: "Introducing eucadev"
description: ""
category: "articles"
tags: [Eucalyptus, "Software Development", "Quality Assurance"]
---
{% include JB/setup %}

Although **eucadev** has been up on [Github](https://github.com/eucalyptus/eucadev) for a month, we wanted the code to become stable before mentioning it publicly. It now feels _sufficiently_ stable to do so. (Though there are still gremlins to work out.)

## What is eucadev?

Currently, **eucadev** fully automates a single-node installation of Eucalyptus from source in a Vagrant-provisioned VM or in an AWS- or Eucalyptus-provisioned cloud instance. Relying on a variety of tools, **eucadev** will

* provision the appropriate VM (CentOS-based for now),
* populate it with required build-time and run-time dependencies,
* build and install Eucalyptus in it,
* configure Eucalyptus for an all-in-one deployment,
* and install and run a small test image.

_A working cloud, on a laptop, with one command!_ If you are a developer interested in working on Eucalyptus or someone who has a need to build Eucalyptus from source for some reason, this is probably the easiest way to get started. Check out the [README](https://github.com/eucalyptus/eucadev/blob/master/README.md) for instructions.

(Those who do not need to build from source are probably better served by the package-based [FastStart](http://www.eucalyptus.com/eucalyptus-cloud/get-started/try#faststart), though it does not have a one-liner option. And those who want to try using Eucalyptus without installing, should sign up for the [community cloud](http://www.eucalyptus.com/eucalyptus-cloud/get-started/try#community-cloud).)

## Inside eucadev

Today **eucadev** is just one shell script, invoked from `Vagrantfile`. That script, however, relies on many tools to do the magic. Logically, it is useful to separate the actions into three phases:

1. **Provisioning phase** involves allocation of resources for cloud deployment. We currently use [Vagrant](http://www.vagrantup.com) to obtain a base CentOS VM either on a local hypervisor, such as [VirtualBox](https://www.virtualbox.org), or in a cloud (AWS or Eucalyptus). In the future, we would like to support bare-metal installations, multiple Linux distributions, and multi-node deployments. The end result should be one or more hosts with base operating system installed.
2. **Installation phase** involves populating base Linux boxes with Eucalyptus software, including changes to the operating system necessary for Eucalyptus components to run. We currently use an [Ansible](http://www.ansibleworks.com) playbook, called [cloud-playbook](https://github.com/eucalyptus/cloud-playbook), to drive from-source installation, along with a few shell commands that should probably become part of the playbook. Chef, Puppet, and SaltStack are other tools also suited for this phase. In the future, we would like to support from-package installation. The end result should be one or more hosts with Eucalyptus software installed and running with default settings (which is not terribly useful until the components are glued together).
3. **Configuration and testing phase** involves registering Eucalyptus components to form a functioning cloud and testing it as a cloud user: running instances, connecting to them, etc. We currently use a combination of [Eutester](https://github.com/eucalyptus/eutester/) commands and shell-outs to [euca2ools](https://github.com/eucalyptus/euca2ools) to drive this process. In the future, the shell-outs should be replaced with Eutester cases, since any tire-kicking cases already exist or should exist in Eutester.

Clear separation between phases is important: it allows different methods to be used at each phase and facilitates concurrent development of the process. We expect that parts of `eucadev.sh` will be absorbed by the technologies that implement  phases, moving **eucadev** closer to its goal of being the glue between the phases: enabling different kinds of installations -- different virtualization platforms, different distros, network topologies, source/package installs -- with simple methods and consistent set of technologies.

## Future plans

The overarching, and perhaps overly ambitious, goal for **eucadev** is to become the standard method for automated deployments of Eucalyptus. Such deployments have multiple use cases, all of which we would like to cover, eventually:

* source-based deployments for developers
* source- and package-based deployments for automated QA
* package-based proof-of-concept deployments (a la FastStart)
* package-based production-grade deployments (inevitably requiring additional configuration)

Currently, the team at Eucalyptus Systems is focusing on the first two, starting with the single-node deployment. But eventually we would like to get to all of them, hopefully with the help of others. Near-term to-do-list looks today as follows:

* package-based installation
* debug `eucadev.sh` glitches that pop up non-deterministically
* move pieces of `eucadev.sh` into the playbook or Eutester, as appropriate
* one configuration file to rule all phases (`Eucafile`)
* multi-node deployments
* doing whatever is needed to facilitate next version of FastStart

## Join us!

The team from Eucalyptus Systems has scheduled once-a-week meetings on Freenode to discuss the state of **eucadev** and coordinate its development:

* **When:** every Wednesday (starting Dec 4th, 2013), 11am - 12pm Pacific
* **Where:** on #eucalyptus-meeting on Freenode

The meetings are open to the public. I hope to see you there! 