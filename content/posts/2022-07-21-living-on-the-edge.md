---
title: "Living on the edge"
description: "How I migrated most of my projects to Deno Deploy"
summary: "How I migrated most of my projects to Deno Deploy"
date: 2022-07-21
aliases: []
categories: ["tech"]
tags: ["deno", "cloud", "edge"]
draft: false
editPost:
    URL: "https://github.com/meacu1pa/meaculpa.dev/tree/main/content"
    Text: "Suggest Changes"
    appendFilePath: true
---

## What is Deno Deploy?

[Deno](https://deno.land/) is an alternative JS/TS runtime to [NodeJS](https://nodejs.org/). [Deno Deploy](https://deno.com/) is the corresponding hosting solution for functions and microservices, currently in Beta. It makes running TypeScript services close to your users (the "edge") really straight forward.

## Why move?

I was sick of maintaining my own server infrastructure, it was too much overhead. Now I have moved my blog and some of my side projects over to GitHub, and am able to continuously deploy to Deno Deploy by pushing to the `main` branch. It's a breeze!

## What are the benefits?

Deno Deploy spins up your projects close to your users, automatically. Since for example my blog is just a bunch of static files, I can serve them with a simple TypeScript server, almost like a CDN. I hope you can feel the speed 💨
