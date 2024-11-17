---
title: How I self-host using Ansible and Quadlets
description: |
    Article relating my experience using Ansible and Quadlets for self-hosting services
date: "2024 November 02"
format: blog_entry
require_prism: true
---

# Introduction

I have a NAS and a VPS running a small set of services that I use often; think services like [Plex](https://plex.tv), [Home Assistant](https://home-assistant.io), [Victoria Metrics](https://victoriametrics.com), [Docker Registry](https://github.com/distribution/distribution), etc.

Recently I wanted to change how I deploy these services to make it easier on myself; this post will go through the changes I made.

But first, a small summary of my setup.

I'm using [AlmaLinux](https://almalinux.org) or [Fedora Server](https://fedoraproject.org/en/server/). To deploy my services I have to either install a RPM package if it exists, or I have to upload a binary on the host and write a systemd service file myself.

While this setup works fine, there are some drawbacks:
1. keeping a service up to date is annoying if you're not installing from a RPM repository
2. if the service is not a simple binary, for example if it needs Node or Python, it can be a pain in the ass to get working without messing the system which is why I usually avoid complex services

I can already hear you saying "What about Docker ?"; hold that thought.

All of this is managed using Ansible with roles and playbooks I crafted for the last 10 years.

# Improving the setup

Yes, containers are the answer to my problems here:
1. an update is one `docker pull` or `podman pull` away
2. everything needed is self-contained and there's no messing with the host system. I can use services written in Python or Node without worrying.

Yes, I could use Docker and it would work fine, but I don't want to.

Partly for technical reasons: I much prefer how Podman containers are integrated with systemd.
But also, this is my personal setup where I get to do what I want, so I'd like to try something new.

Enter [Podman](https://podman.io) and their [Quadlet](https://docs.podman.io/en/stable/markdown/podman-systemd.unit.5.html) concept.

# What is Quadlet ?

Quadlet is a tool included in recent Podman versions that turns declarative _container_ files into systemd units.


