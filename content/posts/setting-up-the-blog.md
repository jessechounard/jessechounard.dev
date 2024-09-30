+++
title = 'Setting Up the Blog'
date = 2024-09-30T10:59:09-05:00
draft = false
+++

In the spirit of journaling my work progress (as a way to look back at what I've accomplished, and to document details I find interesting) I thought it would be a good idea to make it a public blog.

I had a WordPress blog before, and while I like the software and tools quite a bit, I found that it's super annoying to be running on the most popular blogging platform, because it's also the one most targeted by hackers. My old site got hit repeatedly by people finding a way in and changing my posts to include testimonials to products I've never heard of and to add links to other sites to boost themselves. While I'm sure security has improved quite a bit since then, it didn't seem worth it.

After a bit of searching I settled on using Hugo. I'm a big fan of golang html templating. (I read a book called [Let's Go](https://lets-go.alexedwards.net/) on that subject recently that I highly recommend.) I'm sure I'll continue to tinker with it, but for now I've grabbed a simple theme that looked nice ([Hugo PaperMod](https://github.com/adityatelange/hugo-PaperMod)) and it didn't take very long to get it up and running.

For my dayjob, I've worn a lot of hats (webdev in React, mobile using React Native, backend creating AWS Lambda functions) but I've never been involved in creating or configuring any of the infrastructure. So the next parts were super interesting to me. Most of the command line configuration I've had to do was all new to me.

I setup a Digital Ocean droplet. I figure that a shared virtual server should be more than enough for a low traffic blog serving static pages. I learned how to disable logging in with password, and enabled only SSH connections, which I'm told is more secure. I disabled root login entirely, instead requiring a user with sudo access to manually request elevated privileges. Finally, I created another user that does not have sudo access that can deploy updates to the site automatically when I push new changes to github.

That's the plan, anyway. I haven't actually learned how to use github actions to automate that part. For now I'm just doing the Hugo build on my local machine and manually copying the files up to the server.

The next step stumped me for a little bit. I'd read that I should use [nginx](https://www.f5.com/go/product/welcome-to-nginx) as both a webserver and a reverse proxy in case I want to add any other websites or applications to my account without needing additional hosting. While there are infinite articles and tutorials on getting this setup, it's important to note that many of them are a little outdated and that caused me a problem.

Modern web browsers automatically request the `https` version of a website, even if you specify `http` in the address bar. (This is a good thing!) My default installation of nginx was only checking for connections on port 80. So every time I tried to load up the site, it would fail to connect without any reason given, and nothing was showing up on the nginx error logs. I was super confused until I got tired of leaving the terminal to test the website and typed `curl jessechounard.dev` and it magically worked. After a couple of hours of banging my head against the problem, it was amazing to have a path forward, even it it meant I had to go learn another new thing.

My final step in this journey (so far) was to hop over to [letsencrypt.org](https://letsencrypt.org/). I was worried that it was going to be tricky to setup an SSL cert on Digital Ocean (other providers include a web configurator) but with terminal access over SSH, Let's Encrypt uses EFF's [Certbot](https://certbot.eff.org/). It updated my nginx configuration and everything works now. (Which I suppose is obvious, if you're reading this.)

I'm not sure if I'll get around to automating deployment for this little blog. I'm interested, but let's see if I bother to keep watering this plant for awhile first.

Not thrilled with this wall of text. Next topic needs to have some screenshots!