---
title: "I am building a data centre in my house. Sort of."
date: 2026-10-02T22:56:00+10:00
url: "/2026/10/02/i-am-building-a-data-centre-in-my-house/"
draft: false
tags: ["Tailscale", "Home Lab", "Linux", "Docker", "AI", "Remote Development"]
summary: "How an old laptop, Tailscale, SSH and a growing collection of machines slowly turned into my own tiny private cloud."
---

Over the last couple of weeks, I have accidentally started building a data centre in my house. Calling it a data centre is, admittedly, generous. There are no racks, there is no raised floor, and there are no blinking rows of enterprise servers being watched over by people carrying clipboards. There is, however, an old laptop sitting quietly with its lid closed, another computer that is old enough to remember when updating Flash Player was a weekly ritual, a couple of newer laptops, Docker containers scattered around the place, and an increasingly complicated network diagram forming in my head. So I am going with data centre.

It all started with an old Metabox laptop that wasn't doing much. Its WiFi and network interface were dead, so I resurrected it by installing Ubuntu and plugging in a USB WiFi adapter. Once it was alive again, I started wondering what else I could do with a machine that could stay switched on all the time, and the answer, as it turns out, is quite a lot.

## An always-on development machine

My normal development machines are laptops. Laptops are wonderful, but they have an annoying habit of behaving like laptops: you close the lid, carry them somewhere, restart them, install updates, run out of battery, or otherwise interfere with whatever they were doing. For AI-assisted development in particular, I increasingly want the opposite. I want to start a job and leave it running. I want Claude or Codex to work through an issue, build the application, run the tests, start Docker containers, run browser tests, commit the changes and perhaps raise a pull request, without caring whether the machine in front of me is still open.

So the Metabox became an always-on Ubuntu development box. It now runs things such as Docker, .NET, Node, PostgreSQL, Playwright, GitHub tooling and AI coding agents. I can SSH into it, use VS Code Remote SSH, or simply leave a `tmux` session running and reconnect to it later.

This changed the way I think about my laptop. The laptop no longer necessarily has to _be_ the development environment; it can simply be the terminal through which I control the development environment. That distinction turns out to be surprisingly useful.

## Then I discovered Tailscale

The next problem was obvious. A computer that is permanently available at home is only useful if I can reach it when I am not at home. I could have started opening ports on my router, configuring dynamic DNS and setting up firewall rules, and then spent the next few evenings wondering which of those decisions I was eventually going to regret. Instead, I installed Tailscale.

Tailscale creates a private network between my devices, using WireGuard underneath. Once my machines are authenticated into the same Tailscale network, they can talk to each other almost as if they were sitting on the same LAN. There is no port forwarding, no publicly exposed SSH server, and no need to care whether my home IP address changes. That was the point where the whole thing became much more interesting.

My Metabox, my personal Dell XPS, my MacBook Pro, the old Mac mini and my phone can all take part in the same private network, and the physical location of the computers starts to matter much less. My Dell can be on the train with me while the actual build runs on a machine sitting at home. My MacBook can SSH into the Metabox, and my phone can SSH into any of them using Termius. From the point of view of the machines, they are all just there.

## The supermarket test

There is something slightly absurd about standing somewhere completely unrelated to software development, taking out your phone, opening a terminal and typing:

```bash
ssh metabox
```

and then, a few seconds later, running:

```bash
docker ps
```

on a laptop sitting at home. I have now reached the stage where I can be away from the house and still check builds, look at containers, restart a service or reconnect to an AI coding session from my phone. This is not necessarily evidence that I _should_ be doing these things from the supermarket. But I can, and apparently that was important to establish.

## Persistent AI sessions

`tmux` has become another important part of this setup. Normally, if I start Claude Code in a terminal and then close that terminal, I have to think about what happens to the session. With `tmux`, the session belongs to the server rather than to whichever device I happened to use to connect to it. For example, I can create a session:

```bash
tmux new -s claude
```

start Claude inside it:

```bash
claude
```

and detach from the session. Later I might reconnect from another computer:

```bash
tmux attach -t claude
```

The interesting thing here is not `tmux` itself, which has existed for a very long time. What is interesting is what tools such as Claude Code and Codex make possible when they are combined with it. A long-running terminal session is no longer just compiling something or running a script; it can contain an AI agent actively working through a software development task. The machine becomes less like a computer I operate remotely and slightly more like a worker I periodically check in on, and that feels like a fairly significant change.

## The old Mac mini joins the party

Once I started down this road, I remembered that I also had an old Mac mini sitting around. It is roughly twelve years old, with 8 GB of RAM and a 1 TB disk, so it is not going to threaten AWS any time soon. But it runs Ubuntu perfectly well, and so it joined the network too.

The current idea is to give the machines somewhat different responsibilities. The Metabox is better suited to heavier development workloads such as Docker containers, application builds, databases, browser tests and AI coding sessions, while the Mac mini can take on the more boring infrastructure work: monitoring, management tools, backups and services that don't need much CPU. I have Portainer in the mix as well, which gives me a single place to look at the Docker environments running across the different machines.

At this point someone will inevitably suggest Kubernetes. I am trying not to make eye contact with those people.

## My Dell can travel, the environment stays home

This is probably the part I like the most. My personal Dell XPS no longer needs to contain everything; it can travel with me while the long-running development environment stays at home. With Tailscale and SSH, I can connect back to the Metabox and work there, and with VS Code Remote SSH the experience is even more seamless, because VS Code runs locally while the source code, terminal, compilers and development tools all run remotely.

Conceptually, my setup is moving towards something like this:

```text
Dell / MacBook / iPhone
        |
        |
     Tailscale
        |
        +-------------------+
        |                   |
     Metabox             Mac mini
     Ubuntu              Ubuntu
        |                   |
   Development          Infrastructure
   Docker               Monitoring
   .NET                 Portainer
   Node                 Backups
   PostgreSQL
   Claude / Codex
   Playwright
```

The machine I happen to be holding becomes much less important. What matters is that I can reach the compute that is already running.

## Dropbox is starting to look vulnerable

Once you have a few permanently available machines connected through a private network, it is very difficult not to start asking dangerous questions. Why exactly, for example, am I storing all my files in somebody else's cloud? That line of thinking has led me towards adding a NAS. The idea is not to abandon cloud services straight away; it is more about creating a proper storage layer at home that every device can access. Documents, photos, source archives, backups and eventually a much larger collection of data could all live there.

I would still want proper backups, though. A NAS is not magically a backup simply because it contains multiple disks. RAID protects against certain disk failures, but it does not protect against accidentally deleting a folder, corrupted files, ransomware, theft, fire, or me doing something stupid at 1:00 AM. My current thinking is therefore fairly simple:

```text
Working storage
      |
      v
     NAS
      |
      v
External USB backup
```

Cloud backup can come later. One problem at a time.

## Four iPhones are producing quite a lot of data

There are four people in the house, and four iPhones. Over time I would also like the home infrastructure to become the place where family photos and phone backups end up. Again, the attraction is not merely saving on cloud subscription fees. I increasingly like the idea of having a home data platform where I control the storage, and where other services can eventually operate on top of that data. Which, of course, leads to the next dangerous idea.

## Local AI

I have tens of thousands of books and documents, and that number is likely to keep growing. At some point I want to run local AI over that collection. That is not necessarily because I expect a home model to replace the big cloud models, which are extraordinarily capable. The interesting part is having a model close to my own data: a system that can search, classify, summarise and reason over years of documents without everything having to be uploaded somewhere first.

That will probably require more serious hardware eventually, particularly a decent GPU. For now, though, I am more interested in building the infrastructure around it: storage, networking, remote access, containers, persistent compute and backups. Once those pieces exist, adding more powerful compute later becomes much easier.

## And then there is home security

This project is also starting to overlap with something else I have wanted to experiment with, which is home automation and security. Cameras, sensors and other devices could eventually live on their own network and feed into systems running locally. That raises a completely different collection of questions around network isolation, WiFi resilience, storage retention, alerts, and what should keep working if the Internet connection disappears. I have not solved any of those problems yet, which is good, because apparently I needed more hobbies.

## None of this is particularly new

Almost every technology I am using here has existed for years. Linux servers are not new, SSH is certainly not new, and neither are `tmux`, VPNs or Docker. Home labs are definitely not new. What feels different to me is the combination of all these things with AI development tools. Previously, remotely accessing a computer meant that _I_ could operate that computer from somewhere else. Now I can remotely access a computer on which an AI agent has been working for the last hour, and that changes the value of having persistent compute.

The old model was:

```text
I sit at computer -> I work -> computer executes commands
```

The model I am slowly moving towards looks more like this:

```text
I describe work
      |
      v
AI agent works on remote machine
      |
      v
Builds / tests / containers / browser
      |
      v
I reconnect and review
```

The computer no longer has to follow me around.

## My tiny private cloud

There is still a long way to go. The NAS is not there yet, the backup strategy will evolve, local AI needs much better hardware, and home security is mostly still a collection of ideas. I am sure I will break several things along the way. But the basic shape is becoming clear: I am slowly turning a collection of computers that were lying around the house into a small pool of always-available infrastructure.

Tailscale is the thing that ties much of it together. It removes the distinction between "the machine in front of me" and "the machine at home" surprisingly well, and the end result is starting to feel like my own tiny private cloud. It is a cloud where the servers occasionally have their lids closed, one of the nodes is twelve years old, and there is absolutely no SLA.

For some reason, a lot of YouTube videos about home infrastructure have also started surfacing in my feed lately. I am not entirely sure who told YouTube about my new hobby, but watching them is a humbling experience. I am still very much a beginner, and what I have described here is probably child's play for anyone who has been in the home cloud, security and automation space for a while. But I will get there.

I am feeling like a ghost in the machine.
