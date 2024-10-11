---
title: What is an Operating Model and why should you care?
published: 2024-10-11
description: 'Are you still struggling with the basics in your day to day opearations? If you feel like you are constantly putting out fires and not getting ahead, then this might help'
image: './thisisfine.jpg'
tags: [Operating-Model, Cloud, Azure]
category: 'Tech'
draft: false 
---
Well, before we get into it I would like to just acknowledge that this is my first blog on my new site. I'm starting with an ambitious topic that I'm planning on being a series. Let's see how I go!

## Intro
I have been working in IT for a long time now. Both in an opetational capacity and also as a consultant. I have seen a lot of different environments in Azure, some great and some not so great. The ones that are'nt so great all have the same thing in common- They haven't really thought about their operating model.

When it comes to cloud computing, everyone talks about the magic of scalability, flexibility, and cost efficiency. But what’s often missing from the conversation is how businesses actually manage their cloud environments effectively. Its always just jumping straight into workload projects and deploying these in an isolated context, with no real thought about how this will work in the context of the rest of the environment.

 That’s where cloud operating models come in, and if you're using Azure, understanding these models can make or break your cloud strategy.

## Why Should You Care About Cloud Operating Models?
A cloud operating model is the blueprint that defines how your business leverages cloud resources to operate efficiently, how roles and responsibilities are structured, and how you manage things like security, compliance, and governance. It’s not just about getting into the cloud; it’s about staying there securely and efficiently.

Without a well-defined cloud operating model, your Azure environment can become a bit of a Wild West, where teams don’t know who’s responsible for what and how you actually get things done. That leads to inefficiencies, security risks, and extra costs and massive headaches that build up quickly.

## What Is a Cloud Operating Model?
In simple terms, a cloud operating model outlines how you manage your cloud infrastructure and services. In Azure, Microsoft have outlined what this looks like in its cloud adoption faremwork and descrie it in terms of:

* **Governance and Compliance:** Ensuring that cloud resources are used appropriately and that security policies are followed.
* **Security:** Setting up roles and responsibilities for protecting data and applications.
* **Cost Management:** Keeping tabs on spending to prevent surprises at the end of the month.
* **Operational Excellence:** Making sure everything runs smoothly, from deployments to scaling and maintenance.
* **People and Processes:** Aligning your teams, so they know their responsibilities in the cloud world.

While I think Microsoft have described this well in their framework, I think some of the messaging gets lost in all the content, as well as some aspects that need to be adapted to the reality of modern cloud operations or filling in some blanks.

moving on...

## The Three Key Azure Cloud Operating Models ##
The main concept starts with how you organise your teams. Let’s break it down into three basic cloud operating models, each tailored to different business needs:

* **Centralised:** Central operations tend to be the norm for technology environments that consist primarily of stable-state workloads. Examples of stable-state operations include things like commercial-off-the-shelf that have a slow release cadence
* **Decentralised:** Decentralised operations are the least complex of the common operating models. In this form of operations, all workloads operate independently by dedicated workload teams.
* **Enterprise:** Enterprise operations balance the need for control and innovation by simplifying decisions and responsibilities. Central IT is replaced by a more facilitative cloud center of excellence or CCoE team, which supports workload teams.
* **Distributed:** While heavily discouraged, this operations approach might be required for some organisations. The approach mainly relates to organisations that have a loose collection of disparate business units, a diverse base of customer segments, or regional operations.

You can read more on the Microsoft write up [here](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/operating-model/compare#distributed-operations).

## Wrapping Up
A cloud operating model is crucial for optimising your Azure cloud journey. It helps balance the need for agility and control, ensures your team knows what they're doing, and keeps your cloud costs and risks in check. Whether you’re just starting out or already well into your cloud transformation, having the right operating model will help you get the most out of Azure.

The next step? Let's dive deeper. I'm going to try and not regurgitate the Microsoft documentation moving forward, instead focus on either putting it in better terms or adding what, in my experience, is missing from the content.
