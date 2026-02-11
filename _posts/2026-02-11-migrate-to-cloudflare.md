---
title: 'DNS Migration to Cloudflare'
date: 2024-02-11
categories:
  - blog
tags:
  - DNS
  - cloudflare
---
I bought my domain name from now-deceased google domain service. Eversince google terminated the service I had been wanting to move my domain name server (the phonebook where internet users match url addresses with ip addresses) to a different service. 

I finally pulled the plug, and moved mine from google domain (now maintain by squarespace) to cloudflare. At first I did not know that cloudflare provides DNS service too (for free). They are better known as a [CDN provider](https://en.wikipedia.org/wiki/Content_delivery_network) than a DNS service provider.

I signed up for the free tier, and notice that the service they provide has more bells and whistles. It may be fun to play around, but I also realize that since now my website sits behind a CDN, it has some pros and cons.

One of the pros is it should be faster to load my website since now a "copy" of the website now lives in the edge server distributed all over the globe. Additionally, cloudlfare provide more security services like AI Crawl filter and bot filters which may be needed for future-proofing my website.

This, however, comes with a downside, which is I will need to manually tell cloudflare to update the copy of my website that lives in their server whenever I make changes on my website. It is not too complicated but it just add an extra steps on my blogging and website maintenance flow. If this becomes too complicated I may switch back to squarespace domain. 

If I am being honest I am not sure if I will see significant improvement for my website. My website is already light and easy to load due to its [static nature](https://en.wikipedia.org/wiki/Static_web_page). It may improve the rate of image loading but not sure if it will be significant either. Well, we'll see.