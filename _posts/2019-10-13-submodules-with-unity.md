---
layout: post
title: "Submodules with Unity"
date: 2019-10-13 17:00:00 -0600
categories: blog
author: Nathaniel
permalink: /:categories/:year/:month/:day/:title
---

### The Problem

Git has a useful tool for sharing code between repos with submodules. Essentially, submodules are separate git repositories within a repository, allowing for unrelated but useful code to be included in multiple projects. Imagine a large company with multiple projects going on at the same time, with each project using the same underlying API. The code for the API can exist and be maintained in its own repo, and whenever a fix or feature is added, all other projects in the company can receive the new code by simply running the command 

```
git submodule update
```

Neat.

What about Unity? Typically, Unity repositories contain in their root directory folders like Assets, ProjectSettings, Packages, etc., and to include all of this inside a second Unity project can be extremely inefficient or even prevent the project from compiling in the first place. But still, it would be very nice to share common code, tools, and assets across all our projects! How can we do that efficiently?

Thankfully, with clever use of git submodules and symlinks, we can get a stable, efficient solution that allows us to continue developing common code (or any asset, really) separately from the projects that use it, and to reliably share updates to that code with all those projects.

### The Solution

I have two main bodies of code that I’ve created with the intention of speeding up the development of my other projects. These repositories are called TofuCore (general utilities and quality-of-life tools) and TofuUnity (unity-specific tools and frameworks). TofuCore is not a Unity project but TofuUnity is, and includes tests, examples, and other assets to showcase how the code can be used and that it even works in the first place. I really don’t want to include everything in here in my other projects, but I do want bits and pieces.

I can include these projects as submodules easily enough, but to ensure they remain separate from the main Unity project, I can put them in a directory called Submodules in my repos root directory with the commands

```
git submodule add git@github.com:njelly/TofuCore.git Submodules/TofuCore
git submodule add git@github.com:njelly/TofuUnity.git Submodules/TofuUnity
```

(these are private repos, sorry!)

And now, alongside Assets, ProjectSettings, Packages, etc., I have a new directory called Submodules and my Unity project will not be affected at all by their existence. Their contents won’t be included in builds, and they won’t add any extra time to the import or compilation process in Unity. When either of these submodules get updated, I can run the command

```
git submodule update
```

and pull all the changes.

But wait, how do I actually include what I want in my project? That’s where symlinks come in. By creating a symlink to some directory within the submodule, I can make sure that I only get the relevant code and assets that will actually be used.

In addition to the use of symlinks to our submodules, I can put this code in the directory Assets/Plugins/. The Plugins folder is only ever recompiled when changes are made inside of it, which can greatly improve compilation times in our main project. Assuming the code in Plugins has already been tested and will be updated at a slower rate than my main project, this should save a few seconds each time the project compiles, which will add up.

To link to the non Unity project, TofuCore, I navigate to the Plugins folder and run the command

```
ln -s ../../Submoduels/TofuCore/CSharp/TofuCore/
```

If we're on Windows, the identical command would be:

```
mklink /d ..\..\Submodules\TofuCore\CSharp\TofuCore\
```

Now to link to only the relevant code in my other Unity project, I make a new directory Assets/Plugins/TofuUnity, navigate to there, then run the command

```
ln -s ../../../Submodules/TofuUnity/Assets/Scripts/Animation/
```

Or on Windows:

```
mklink /d ..\..\..\Submodules\TofuUnity\Assets\Scripts\Animation\
```

### In Conclusion

Now we have a stable, efficient solution for our initial problem: sharing common code and assets among multiple Unity projects.

But that’s not all!

This solution also allows private code (like my TofuUnity and TofuCore repositories) to stay private, even if the rest of the source for the actual game is shared publicly. Very handy for devblogs, in my opinion.

As an example, [you can view my public repository, GridRPG][gridrpg-repo].

Hope you found this useful!

[gridrpg-repo]: https://github.com/njelly/GridRPG
