---
title: 'DNS Migration to Cloudflare'
date: 2026-02-11
categories:
  - blog
tags:
  - DNS
  - cloudflare
---
I bought my domain name from now-deceased Google domain service. Eversince Google terminated the service I had been wanting to move my domain name server (the phonebook where internet users match url addresses with ip addresses) to a different service. 

I finally pulled the plug, and moved mine from Google domain (now maintain by squarespace) to cloudflare. At first I did not know that cloudflare provides DNS service too (for free). They are better known as a [CDN provider](https://en.wikipedia.org/wiki/Content_delivery_network) than a DNS service provider.

![image-center](/assets/images/blog/cloudflare.png){: .align-center}

I signed up for the free tier, and notice that the service they provide has more bells and whistles. It may be fun to play around, but I also realize that since now my website sits behind a CDN, it has some pros and cons. One of the pros is it should be faster to load my website since now a "copy" of the website now lives in the edge server distributed all over the globe. Additionally, Cloudflare provide more features like security services (AI Crawl filter and bot filters which may be needed for future-proofing my website) and better web analytics (may be even better than Google Analytics). This, however, comes with a downside. I will need to manually tell cloudflare to update the copy of my website that lives in their server whenever I make changes on my website. It is not too complicated but it just add an extra steps on my blogging and website maintenance flow. If this becomes too complicated I may switch back to squarespace domain.

![cloudflare-analytics](/assets/images/blog/cloudflare-analytics.png){: .align-center}

If I am being honest I am not sure if I will see significant improvement for my website. My website is already light and easy to load due to its [static nature](https://en.wikipedia.org/wiki/Static_web_page). It may improve the rate of image loading but not sure if it will be significant either. Well, we'll see.