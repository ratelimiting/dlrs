---
title: "Fingerprint simulator "
date: 2026-02-24
draft: false
tags: ["cybersecurity", "MERN", "docker", "biometrics", "cve", "exploit", "vulnerability", "opsec", "nist"]
---
There's a common misconception about biometrics and cybersecurity, this misconception is that you can't get infected or compromised just by visiting a site.

Spoiler alert: You can.

I built this platform to demonstrate to some extent what a malicious website or a rogue ad can gather from a user simply by having them load the page--and what a threat actor can do with that information.

Most fingerprinting projects stop at "this is what I see of you." Mine goes all the way to the CVE. It's an educational platform that combines simulation with real analysis, and it has two general sections.

### 1. The visitor-tracking simulator

For legal and ethical reasons, the simulator's data does not come from real people: every 10 seconds a synthetic visitor is generated, each one with a coherent identity (city, ISP, timezone, language, IP) and its own canvas, WebGL, audio and fonts. A small percentage of those visitors are made susceptible to CVEs and/or database leaks on purpose. Each visitor goes through the full pipeline:

- **Similarity comparison** against previous visitors.
- **Browser vulnerability check**; if one is found, an exploit is shown demonstrating step by step how that vulnerability can be exploited.
- **Leak check**: the IP is searched against a breach database and, if it appears, the information associated with the data breach where that IP was found is displayed.

The application grabs the browser version and checks if there are any CVEs associated with it, showcasing the importance of keeping your software up to date. Worth clarifying: many browsers nowadays freeze the version they report in their User-Agent to shrink the fingerprinting surface; even so, that frozen version is still useful for looking up vulnerabilities when the browser is severely out of date. That's why the app tries to recover the real full version through Client Hints (`navigator.userAgentData.getHighEntropyValues`), and when it can't, it tells you how to find it manually at `chrome://version`.

On IP-based leaks: in most cases this could seem kind of useless because most people don't have a static IP, but some of the time users might have a static IP without asking for it and/or knowing the security implications of having one. A static IP acts as a persistent identifier, making it trivial to tie historical data leaks directly to the user.

One of the reasons I built this site was to showcase how unique each user's browser fingerprint is. Initially I made the app compare every new visitor against the whole database of past users to see the probability that the user had visited the site before. However, thanks to the amount of users generated, this caused a significant server load and a speed bottleneck, which is why I switched the logic to compare only against the most recent user, keeping the application fast and responsive.

To make the information easier to read, I made a dashboard that displays general statistics and a real-time table. The table shows all the information gathered from the user, including CVEs, a step-by-step on how they can be used, and IP data-leak searches.

### 2. The real scan

The second section is a real scan of the visitor. It inspects the visitor's fingerprint and displays the information the page is able to gather about them: network, hardware, canvas, WebGL, User-Agent, and so on.

A button checks whether the user's browser has any known vulnerabilities through the NIST API (NVD). The search distinguishes between an exact version match and a browser-level match, and none of it fires or gets saved automatically.

The idea is not to track anyone. That's why real visitors' information is not stored in any database, and for the same reason no IP check is performed, since it's a highly identifying piece of data. I chose not to use any external API for the IP check: I can be certain that I won't store the information, but I can't say the same about a third-party API.

### Architecture

Stack: React 19 + Vite 7, Express 5, MongoDB, Docker behind Nginx, i18next. I used Docker to containerize the application and hosted it on a VPS.

### See it live

You can see the simulator in action here **[fp.dlrs.me](https://fp.dlrs.me)**
