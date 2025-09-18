---
title: If you built something but didn't blog about it, did you really build it?
slug: if-you-built-something-but-didn-t-blog-about-it-did-you-really-build-it
date: 2025-09-18T09:15:52+01:00
draft: false
tags: [Go, Hugo, GitHub-Actions, Netlify]
description: "A write-up about building my blog site and the automated pipeline from Obsidian to deployment"
---

After building my portfolio site and not doing any form of automation for deployment or easy ways of adding projects etc, I knew that any content sharing sites I'd be doing in the future would have to have some form of low barrier to entry update system. 

So when I decided that I should make a blog and document the things I'm working on so that I can yell out into the void for validation, I knew that I had to make some automated pipeline for it. Otherwise, I would simply not update the blog and then what's the point. 

Initially I was planning on using Ghost for building a blog as I encountered it during one of the boot.dev courses, but I just couldn't find the motivation. Absolutely nothing against Ghost, looks like a very solid product, but I think it didn't quite hit what I wanted to do. That's when I came across NetworkChuck's [video](https://www.youtube.com/watch?v=dnE7c0ELEH8), and that fit just right. 

- [x] low barrier to entry for writing
- [x] automation
- [x] easy UI with possible customisation

It was exactly what I was looking for. 

TL;DR of the video: write in Obsidian; sync a subdirectory into a Hugo repo; build to static pages; push to GitHub; deploy to a VPS from a separate branch. 

I wanted to do something slightly different, I've been wanting to use hosting platforms for a while as I felt it's something that I should have at least some experience with. So I looked into Vercel and Netlify. I wanted to stay within the free tiers, especially since I probably will be wanting to build a few more things and host them so the less usage for this the better. I was going to use GitHub to store the content anyway, and I have some exposure to GitHub Actions, so I may as well use those to build my Hugo project and then deploy to wherever I'm hosting from there. Good practice for writing workflows anyway, right? 

So how do I make it so that only a part of my Obsidian vault gets used for the blog; in the video Chuck used a Python script for syncing the two locations. I looked into it, I thought maybe symlinks, but all the suggestions I came across were more effort than they were worth. After all, it's just text files really, so they won't take up much space even if they're duplicated. So I wrote my own script for performing the sync in Go, which will be useful later as I can compile it and add it to the path for ease of use.
Simple script really, hard-coded vault and repo locations, repo content gets removed on call, then it walks the vault subdirectory for all the files and dirs, then clones them over to the repo. No need for anything fancy since it's just a tool for myself and it doesn't need to be super robust. 

Okay so, I have my vault and template setup, I have a script to sync the repo and vault. I mess around with the way the Hugo template looks - I'm almost sure this will change at least 8 more times - but now it's time to figure out the builds and deploys.

I wasn't sure whether Vercel or Netlify were going to be better for this, so truthfully I just arbitrarily picked one, that being Netlify. I wrote a simple GitHub Actions workflow to build the content. It worked, luckily [peaceiris](https://github.com/peaceiris/actions-hugo) released a GitHub action which made this a breeze, the official docs suggested a far more complex and long process for building and deploying with setting everything up step by step. But why do that when someone was generous enough to abstract it for me.

So building is done, now to go and set up Netlify. To confirm everything was working fine, I downloaded the artifacts from GitHub and drag-and-dropped them onto Netlify and it worked... At least partially, the front page worked but the links to sections redirected to an incorrect address, I may have preemptively set up the baseURL in Hugo to my subdomain. A quick rebuild and drag-and-drop fixed that. So now that it's live and I know the build works properly, I set up the deploy steps in Actions and it gets one shot. 

Now then, just the finishing touches left. I bought a domain a while ago, and it was just sitting there, so it's time to put it to use. I went to my domain manager dashboard, added a verification TXT, all was good, didn't add any DNS records cause it said the step was optional, didn't move the nameservers because I saw that you didn't need to. So I finish the setup and wait. Nothing is happening, and I get the feeling that I may have messed it up a little. So, time to move the nameservers! I do that and something happens! But it still doesn't work, so I go look some stuff up. I find another place in the project dashboard, by clicking the massive and obvious "Go to Domain management" button, which I somehow missed, which prompted me to update the site’s domain, so I did. 

![](domain%20management.png)


And I see a little bit more movement, now we're cooking. The app is somewhat live on the subdomain? But the certificates are failing, did a bit more browsing and saw that I needed to add an A record, so I give that a go. 

Now, I don't know whether it was just a timing thing that everything sorted itself out behind the scenes just as I finished doing all these steps or whether I just kinda stumbled into the correct things I had to do. But alas, it's alive! 

And so, now begins my journey of learning how to write proper blogs rather than whatever this post is, while hopefully bringing some entertainment to those who stumble across this. 

If you want to check out the repo for this blog, you can do so [here](https://github.com/RobertGolawski/blog). All feedback welcome, and if you see any improvements feel free to open a PR. I think that could be fun. 