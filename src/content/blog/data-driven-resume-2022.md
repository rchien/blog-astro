---
title: Data-Driven Resume in 2022
author: Richard Chien
pubDatetime: 2022-02-06
postSlug: dd-resume-2022
featured: false
draft: false
tags:
  - resume
  - data-driven
  - tech
description: "Data-Driven Resume in 2022"
---

# Data-drive resume in 2022

It’s good to keep resume updated. I typically do it every two years. The act serves both as checkpoint and trigger to update my engineer log. While I usually stick to the same format for 5+ years, 5 years is eternity in tech and it is, after all, 2022. Surely there must be a few competing, open-sourced, versatile resume builder based on open standards?

<aside>
💡 tl;dr Unfortunately there does not exist an ideal solution IMO but not without hope. Both jtex and LuaTeX via TeXiFy seem like reasonable approaches. In all cases, one would still need to modify OSS templates. In other words, LaTeX templates require modifications to be templatized.

</aside>

# One Goal

My goal is `simple`. Find a OSS resume building framework where

1. Content (work history, skills, and etc) are stored in open standard without formatting.
2. Ability to re-use various awesome OSS templates. Types of format of templates do not matter much. Availability is key.
3. Option to manage contents offline as resume contents are highly sensitive.

Simple right? 🤣

# Then Comes the Research

With a bit of Internet research, I quickly realize there are roughly three existing options.

## 1. Free online resume builder

There are [few](http://www.resumake.io) [OSS](https://gitconnected.com/tools) attempts at resume builder app, there are at early stages and often lack features or templates.

## 2. Not so free online resume builder

Many (in fact, too many) paid alternatives do exist but the price premium cannot be justified for someone who updates resume at most once a year. Not to mention the difficulty exporting my personal data. I don’t want my highly personal content stored in some vendor specific database where I have little or no control over.

## 3. OSS Frameworks

Do-it-yourself OSS frameworks like [JsonResume](https://jsonresume.org/schema/) looks like good starting point. While the schema is sufficiently good, its theme eco-system are lacking IMO. Each theme only has to implement few interfaces like render() which returns HTML. Each theme is free to use whatever CSS/HTML framework that pleases them. This is both a strength and weakness. LaTeX and PDF support are done through [pandoc](https://pandoc.org/) and generally don’t look as nice as native LaTeX templates.

# Charting My Own Path

![https://imgs.xkcd.com/comics/standards.png](https://imgs.xkcd.com/comics/standards.png)

Unfortunately there are no existing solutions that satisfy all our goals. While there are a few resume builder apps that accept JsonResume schema, they are not very polished and has limited selection of templates. Most importantly, they do not work well with Latex templates out there. The mature apps are expensive, requires manual data entry, and data lock-in.

## Working backwards

If my goal is to have a nice looking resume based on OSS templates (i.e. [awesome-cv](https://github.com/posquit0/Awesome-CV)), I would need to bolt on templating engine to LaTeX. [JsonResume schema](https://jsonresume.org/schema/) looks like good starting point for the content portion.

Here are the two ways to solve this I can think of

## JSON in LaTeX

This approach requires specific flavour of LaTeX engine like LuaTex that enables [loading JSON](https://tex.stackexchange.com/questions/395982/load-fields-from-json-file-using-lualatex) and [foreach](https://tex.stackexchange.com/questions/149162/how-can-i-define-a-foreach-loop-on-the-basis-of-the-existing-forall-loop) over specific elements to dynamically construct LaTeX elements. This engine requirement does not bode well with Overleaf which hapens to be one of the best LaTex collab app. The lack of support is obvious due to the capabilities of LuaTeX. There is [TeXiFy](https://plugins.jetbrains.com/plugin/9473-texify-idea) plug-in for IntelliJ IDE which supports LuaTex and has many useful features like auto-complete and many other convenience features.

**Pros**

- Data-driven resume
- No additional steps to render (provided the environment supports LuaTex)

**Cons**

- Requires LuaTex support which is uncommon. Overleaf, for instance, does not support this due to inability to maintain sandbox environment.

## Generate LaTeX with templating

This approach involves embedding string template markups (i.e Jinja2) in LaTeX and then run a pre-process step, when paired with Json data, yields the final LaTeX. As of Q1 2022, [jtex](https://github.com/curvenote/jtex) is actively maintained, OSS CLI that does exactly this. The software is currently undergoing some refactoring and did not work well with LaTeX templates out there in the wild but I expect situation to improve in Q2.

**Pros**

- Data-driven resume
- Final LaTeX acts like any regular LaTeX package and can be easily edited on overleaf
- Does not require special LaTeX packages or compilers

**Cons**

- Pre-process steps need to happen on client machine or self-hosted server.

IMO there’s no perfect solution. Both approach has their pros and cons. As of January 2022, I gravitate towards [2] where we use templating to generate the final LaTeX. Mind you this is nothing new as [many have made similar attempts](http://wtbarnes.github.io/2016/08/28/cv-howto/). Rather than creating yet another variant, I would actually contribute to JTex as it seems to be actively worked on. However, this will not be my focus so will re-visit in Q4 2022.

<aside>
💡 For now I’ve updated my good-old resume in Google Doc and that will be that for now. 😂

</aside>

# Resources

- [Another attempt at RaC](http://wtbarnes.github.io/2016/08/28/cv-howto/)
- [List of decent OSS LaTex templates](https://zety.com/uk/blog/latex-cv-templates)
- [JTex](https://github.com/curvenote/jtex)
- [Intellij TeXiFy](https://plugins.jetbrains.com/plugin/9473-texify-idea)
- [Resumake.io](notion://www.notion.so/www.resumake.io) has limited selection of awesome templates. However, this project is not in active development. The templates are also modified in non-extensible manner. IMO, adding of new templates would be slow unless a re-architect takes place.
- [Learn LaTeX](<https://www.overleaf.com/learn/latex/How_to_write_a_LaTeX_class_file_and_design_your_own_CV_(Part_1)>)
