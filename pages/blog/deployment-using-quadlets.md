---
title: How I self-host using Ansible, Quadlets and Tailscale
description: |
    Article relating my experience using Quadlets for self-hosting services
date: "2024 November 02"
format: blog_entry
require_prism: true
---

# Introduction

I have a personal network of devices (computers, NAS, VPS, smartphones) running a small set of services that I use often; think services like [Plex](https://plex.tv), [Home Assistant](https://home-assistant.io), [Victoria Metrics](https://victoriametrics.com), [Miniflux](https://miniflux.app), etc.

Now a small summary of my setup to manage and use all this.

## Deployment

To deploy these services I have to either install a RPM package if it exists, or I have to upload a binary on the host and write a systemd service file myself.
While this setup works fine, there are some drawbacks:
1. keeping a service up to date is annoying if you're not installing from a RPM repository
2. if the service is not a simple binary, for example if it needs Node or Python, it can be a pain in the ass to get working without messing the system which is why I avoid it

I can already hear you saying "What about Docker ?"; hold that thought, we will get into this later.

## Networking

I have two ways to access my services:
1. directly on the LAN for a subset of services
2. using a VPN

I use [Tailscale](https://tailscale.com); this gives me a _tailnet_ where every device is accessible and then it's just a matter of making any service listen on the tailscale network interface on this device.

This is the easiest and most straightforward way to host something on the tailnet. For example if I want to use Plex on my NAS called *bespin* I can just go to `bespin.tail12abc.ts.net:32400` (`tail12abc.ts.net` being my tailnet; this assumes [MagicDNS](https://tailscale.com/kb/1081/magicdns) is enabled). It looks like this:

![basic setup](./deployment-using-quadlets/basic_setup.avif)

There is another way to host services on a tailnet: give each service its own key and name, essentially making it visible as a "machine" on your tailnet. To continue with my Plex example, I could have a machine named `plex` and then access it at `plex.tail12abc.ts.net:32400`.
You can go even further by using a Tailscale-aware reverse proxy (I use [Caddy](https://caddyserver.com) with the [caddy-tailscale](https://github.com/tailscale/caddy-tailscale) plugin) to make it accessible at `plex.tail12abc.ts.net` without messing around with ports.
To me, this feels like the best way to do it, if only because it "looks neat".

There is, however a problem with this: almost no service is Tailscale-aware so how do I _actually_ make a service listen on its own Tailscale interface ?

Well, this can be done in two ways:
1. with a reverse proxy like I mentioned above; the reverse proxy is responsible for creating the Tailscale device for each service
2. start a Tailscale daemon just for this service

Currently I'm only using the first option because it fit my use cases, for example I wanted to host a Docker registry and have a "pretty" URL like `registry.tail12abc.ts.net`. It looks like this:

![reverse proxy setup](./deployment-using-quadlets/reverse_proxy_setup.avif)

But I'd like to have my VictoriaMetrics instance accessible at `victoria.tail12abc.ts.net:8428` without going through my reverse proxy because it's an unnecessary indirection in this case: this is something I can't currently do well with my deployment setup using only systemd services.

## Improving the setup

TODO

# What is a Quadlet ?
