---
layout: post
title: "Addressables in Unity"
date: 2019-07-11 23:00:00 -0600
categories: blog
author: Nathaniel
permalink: /:categories/:year/:month/:day/:title
---

Unity has a fancy new system for loading assets in [Addressables][addressables]. The idea seems to be that assets can be loaded asynchronously with a simple string address, without your code caring where the asset came from. It’s still in preview as I’m writing this, for what that’s worth.

Asynchronicity obviously complicates things, so I’ve been trying to work out my own solution. Instantiating everything inside a callback is lame and sounds like boilerplate hell, so I’d like to load what I need, cache it, access it synchronously, and release it when I’m done like a responsible programmer.

Here’s what I’ve come up with, I’ll be updating it as I run into any issues:

<script src="https://gist.github.com/njelly/3a2eb0c461b90ad9392cdfe2b866f275.js"></script>

[addressables]: https://docs.unity3d.com/Packages/com.unity.addressables@0.4/manual/index.html
