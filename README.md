# CTF Write-up: ideh.casa — OSINT Challenge

**Write-up Author:** Amine Brihi (AKA NOBODY404)  
**Challenge Authors:** Dreekos, BSxNinja, Cyber Command  
**CTF:** IDEH-CTF-2026  
**Category:** OSINT  
**Challenge:** ideh.casa  
**Difficulty:** ⭐⭐⭐☆☆ (Medium)

---

![Challenge Prompt](assets/screenshot_prompt.png)

## 📋 Challenge Overview
> “I heard you are good at OSINT, is it true?”

![Header](assets/hacker_header.png)

Simple prompt. Intimidating silence. Classic OSINT mind games.

When I first opened this challenge, all I had was a domain name and a question that felt like a dare. No file to download, no obvious entry point — just pure reconnaissance work ahead. As someone who loves digging through digital breadcrumbs, I knew this was going to be fun.

**Spoiler alert:** The website was dead, but its ghost told me everything I needed to know. 👻

---

## 🔍 Step 1: DNS Reconnaissance — Know Your Target
First rule of OSINT: understand what you’re dealing with. I started with basic DNS enumeration to map out the infrastructure:

```bash
dig ideh.casa ANY
nslookup ideh.casa
```

### 🎯 DNS Results:
![DNS Results](assets/dns_results.png)
![DNS Results](assets/dns_results_2.webp)


**Key Findings:**
- **IP Address:** `165.227.4.252` (DigitalOcean droplet)
- **Name Servers:** Generic registrar servers
- **RFC8482 HINFO:** Modern DNS servers returning minimal ANY query response

### Testing Subdomains
I wrote a quick bash loop to check if common subdomains revealed anything different:

```bash
for sub in www mail ftp admin blog dev test api portal; do
    echo -n "$sub.ideh.casa: "
    dig +short $sub.ideh.casa
done
```

**Result:** Every subdomain pointed to the same IP (`165.227.4.252`). This is a wildcard DNS configuration—all subdomains resolve identically. Nothing special here, but good to confirm there's no hidden subdomain with unique content.

![Subdomain Recon](assets/screenshot_subdomains.png)


---

## 🌐 Step 2: Web Access Attempts — Into the Void
Time to visit the website and see what we’re working with:

```bash
curl -v http://ideh.casa
curl -v https://ideh.casa
curl -I http://165.227.4.252
```

### 💥 The Plot Twist:

![Port 80 Unresponsive](assets/screenshot_port80.png)
![Port 443 Unresponsive](assets/screenshot_port443.png)
![IP Unresponsive](assets/screenshot_ip_check.png)

Both ports 80 and 443 were completely unresponsive.

At this point, most people might think “dead end,” but in OSINT, a dead website isn’t a roadblock — it’s a signpost pointing to the archives. 🗺️

---

## 🔎 Step 3: Username OSINT — Following False Leads
The domain name “ideh” could easily be a username. Maybe the challenge creator had a digital footprint somewhere? I fired up Sherlock to enumerate potential profiles:

```bash
sherlock ideh
```

### 📊 Sherlock Results:
110+ potential profiles! But quantity doesn’t equal quality. I needed to verify the most promising leads.

![Sherlock Scan 1](assets/screenshot_sherlock_1.png)
![Sherlock Scan 2](assets/screenshot_sherlock_2.png)


**Analysis (GitHub Example):**
- Account created in 2013 (way before this CTF)
- Zero repos, zero gists, zero followers, zero following
- Last updated in 2016 (completely dormant for 10 years)

![GitHub Profile Analysis](assets/screenshot_github_check.png)


**Verdict:** Generic username, not related to this challenge (Red Herring 🎣).

---

## 🕰️ Step 4: The Wayback Machine — Digital Archaeology
Here’s where the real OSINT magic happens. If the website is down now, maybe it was alive before.

### Checking for Archived Snapshots:
```bash
# API check
curl -s "http://archive.org/wayback/available?url=ideh.casa" | jq

# Or manual visit
https://web.archive.org/web/*/ideh.casa
```

### 🎯 Discovery Time!
**JACKPOT!** The Wayback Machine revealed a snapshot from **Dec 28, 2025**.

- **Archived URL:** [web.archive.org/web/20251228124406/https://ideh.casa/](https://web.archive.org/web/20251228124406/https://ideh.casa/)

![Wayback Machine Snapshot](assets/screenshot_wayback_1.png)
![Wayback Machine Result](assets/screenshot_wayback_2.png)


---

## 🚩 Step 5: Flag Extraction — The Final Reveal
I navigated to the archived snapshot and found the flag sitting right there in plain text on the archived homepage.

![Flag Reveal](assets/screenshot_flag.png)


### 🎯 Flag
```text
IDEH{archived_but_n0t_f0rgott3n}
```

---

## 💡 Key Takeaways
- **The Wayback Machine is Your Best Friend:** Historical data is often more valuable than current data.
- **Automation Saves Time, But Verification is Key:** Sherlock gave 110+ results, but manual verification filtered out the noise.
- **Dead Ends Are Clues:** An unreachable website with valid DNS screams “check the archives!”
- **Pattern Recognition:** In CTFs, nothing is truly random.

---

## 🛠️ Tools Used
- `dig` / `nslookup` — DNS reconnaissance
- `curl` — HTTP request testing
- `Sherlock` — Username enumeration
- `Wayback Machine` — Internet Archive (🔑 MVP)
- `jq` — JSON parsing

---

## 🎤 Final Thoughts
This challenge perfectly encapsulates what OSINT is all about: persistence, creativity, and knowing where to look. The flag wasn’t hidden behind complex cryptography — it was sitting in plain sight in the past.

The flag name `archived_but_n0t_f0rgott3n` is poetic—it reminds us that in the digital age, deletion is an illusion. Everything leaves traces, and a good OSINT investigator knows exactly where to find them. 🕵️‍♂️

**Special shoutout to Dreekos, BSxNinja, and Cyber Command for creating this educational challenge!**

Remember: In OSINT, the past is never truly past. Keep digging, keep learning, and never forget to check the archives! 🔍🚀

---

## 📞 Connect With Me
- **Medium:** [NOBODY404](https://medium.com/@n0b0dy4o4/ctf-write-up-ideh-casa-osint-challenge-05e83ca70f49)
- **LinkedIn:** [amine-brihi](https://www.linkedin.com/in/amine-brihi)

*Writeup created with ❤️ by NOBODY404 | January 2026*
