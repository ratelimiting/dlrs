---
title: "Fingerprint simulator "
date: 2026-02-24
draft: false
tags: ["cybersecurity", "MERN", "docker", "biometrics", "cve", "exploit", "vulnerability", "opsec"]
---
There's a common misconception about biometrics and cybersecurity, this misconception is that you can't get infected or compromised just by visiting a site

Spoiler alert: You can.

I built this fingerprint simulator to demostrate to some extent what a malicious website or a rogue ad can gather from an user simply by having them load the page--and what a threat actor can do with that information.

For legal reasons and ethical reasons I had to go from real visitor data to generating a fake visitor every 10 seconds. All of this because this project showcases all the information gathered to all the public, displaying all this information from real users would be a privacy nightmare, moreso considering that I actually check for CVEs (Common Vulnerability and Exposures), and I check their IP against known data leaks.

### How does the Vulnerabilities and Leaks are seen

The application grabs the browser version and checks if there are any CVEs associated with it. Showcasing the importance of having your software up to date.
The IP is used to search for data leaks, in most cases this could be kind of useless because most people don't have a static IP but some of the times users might have a static IP without asking for it and/or knowing the security implications of having one. As an static IP acts as a persistent identifier, making it trivial to tie historical data leaks directly to the user.

### Data visualization

To make the information more easy to see, I made a dashboard that displays general statistics and a in real-time tabl, the table displays all the information gathered from the user, including CVEs, a step by step on how can they be used and IP dataleak search.

One of the reasons why I built this site was to showcase how unique the browser fingerprint from one user to another is. Initially I made the app compare every new visitor against the whole database of past users to see the percentage of probability that the user has visited the site before. However, thanks to the amount of users generated this is causing a significant serverload, and a speed bottleneck, that is why I switch the logic aginst the most recent user, to keep the application responsive.

### Architecture

I built this application using the MERN Stack (MongoDB, Express, React and Node). I used docker to containerize the application and I hosted the app on a VPS. 
Every 10 seconds a new simulated user is generated, the fingerprint information is randomized, and a small percentage of those users are susceptible to CVE's and/or database leaks purposely. 

### See it live

You can see the simulator in action here **[fp.dlrs.me](https://fp.dlrs.me)**