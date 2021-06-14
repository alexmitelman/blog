---
title: "How I Migrated this Site to Cloudflare"
date: 2021-06-14
draft: false
tags: ["blog", "cloudflare", "hugo"]
TableOfContents: true
author: "Alex Mitelman"
---

I've migrated my website from GitHub Pages to Cloudflare Pages. I've also moved my domain from Namecheap to Cloudflare. I have already used Cloudflare Web Analytics instead of Google Analytics. The reasoning behind this is following:

[Cloudflare Web Analytics](https://www.cloudflare.com/web-analytics/). I didn't like the idea of Google tracking my website visitors. Google Analytics sets a cookie. This probably means that such sites need to put that annoying cookie disclaimer that the EU came up with (out of good intentions, of course). And I'd like to avoid that. Cloudflare Web Analytics doesn't set any cookie to site visitors. Cloudflare also claims to care about privacy and not to profit from users' data.

After switching to Cloudflare Web Analytics I've seen bigger numbers of my website visits. Maybe the explanation lays in people using ad-blocking browser extensions that are known to block Google Analytics. Nevertheless, some ad-blockers started to block Cloudflare Web Analytics as well, although apparently, this is not as widespread yet. Paid Cloudflare customers can use edge analytics instead. It doesn't use any client-side JavaScript unlike the free version and Google Analytics, of course. This means that no browser extension can block such analytics as it's collected on the server-side.

I also enjoy the nice and clean interface of the Cloudflare solution. As a drawback, it doesn't support UTM tracking. I don't use such tracking but be aware if you are.

[Cloudflare DNS](https://www.cloudflare.com/dns/). Initially, my domain was parked with Namecheap - the service I've used to purchase the domain. It worked OK until I had to authorize my domain for Mailchimp - another service I use to send out the newsletter. Some of my subscribers noticed that the newsletter emails end up in quarantine. Most likely this happens because emails are being sent on behalf of Mailchimp. It looks like this in the Gmail interface.

![/images/blog/migrate-to-cloudflare/Untitled.png](/images/blog/migrate-to-cloudflare/Untitled.png)

Securing emails with [DKIM](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail) should help to solve this. It was created specifically to prevent email spoofing, when SMTP could send emails with any sender address, even from a completely different domain. To set up DKIM within Mailchimp we need to authorize the domain. And this is where I faced an issue with Namecheap. Although I seemed like doing settings right on the Namecheap DNS panel, Mailchimp wasn't able to see those changes even after 48 hours - a usual wait time for DNS changes.

Migrating the domain to Cloudflare turned out to be super easy. Surprisingly, Namecheap's [help page on migration domain to Cloudflare](https://www.namecheap.com/support/knowledgebase/article.aspx/9607/2210/how-to-set-up-dns-records-for-your-domain-in-cloudflare-account/) turned out to be better than [Cloudflare's one](https://developers.cloudflare.com/registrar/transfer-instructions/namecheap). After that, I was able to authorize the domain with Mailchimp. From now on, subscription letters look like this.

![/images/blog/migrate-to-cloudflare/Untitled1.png](/images/blog/migrate-to-cloudflare/Untitled1.png)

However, there is another surprise: Cloudflare does not support email forwarding. And this is the feature I've used on Namecheap to forward emails from my shiny domain to an old Gmail account. Cloudflare is rapidly expanding its services but for some reason, the email forwarding is not one of the services they provide. I ended up using a third-party service called [ImprovMX](https://improvmx.com/). It has a free tier which is enough for my needs right now.

Any changes made to Cloudflare DNS are blazingly fast. No more "Please wait 24 to 48 hours". Any DNS changes I've made through Cloudflare were reflected immediately.

[Cloudflare Pages](https://pages.cloudflare.com/). My website is built with Hugo static site generator. This means that the site is completely static, with no server-side logic. Initially, I've used GitHub pages  - simple and somewhat obvious solution for a static dev website. At some point, I've started experiencing SSL issues to an extent that some of my subscribers emailed me about it. Weirdly enough, only one of four GitHub Pages IPs had this SSL certificates problem. I've decided to give Cloudflare Pages a try as I was looking into it for quite some time and it gained my interest.

Setting up Cloudflare Pages turned out to be almost easy. We permit it to access a GitHub repo. Build process is triggered on every commit to the repo. Cloudflare Pages downloads the appropriate Git branch and runs Hugo to generate the website pages. There is one quirk, though. By default, Cloudflare Pages uses a very old Hugo version, so my build was failing. Setting up an environment variable to use a specific Hugo version helped to solve this issue.

Cloudflare Pages uses HTTPS out of the box. More importantly, Cloudflare is famous for being a CDN provider. This means that the website is being served from the various edge servers which are always close to the client. This promises a better user experience with faster page loads.

I'm excited to consolidate most of the services I use for my website within one service provider. I'm looking forward to trying out more services by Cloudflare. For example, there is [Cloudflare Registrar](https://www.cloudflare.com/products/registrar/) with the motto "You pay what we pay". This means that we can register and renew the existing domain at a wholesale price.