# n8n hosting: what it really costs, five checks most lists skip, and when a cheap VPS is the smarter buy

n8n costs nothing to run. The workflow engine is open source, executions are unlimited, and the only bill you'll ever see is for the machine it lives on. That's why searches for n8n hosting are really asking one question: where should I put this thing, and what's a fair price to pay?

Short answer first. Managed n8n hosting currently runs from about $3.70 to $25 a month. A DIY VPS goes lower — Hetzner's CAX11 in Europe is €3.29 a month for 2 vCPU and 4 GB, and budget US providers like RackNerd sell annual KVM plans that work out to roughly $2 a month once you pick a tier with enough RAM. The official n8n Cloud starts at €20 a month, billed annually.

Price is still the wrong first filter, though. n8n rarely breaks because the server is slow. It breaks because webhooks can't reach it, because a rebuild wiped its encryption key, or because the platform put your workflow to sleep. One r/selfhosted thread that made the rounds described exactly this: two weeks of webhooks failing overnight, noticed only when a client asked where his invoice had gone. A cheap server that misses triggers is more expensive than an expensive one that doesn't.

This guide covers both halves: what n8n actually needs from a host, and what each route costs once the intro pricing wears off.

> **Quick picks**
>
> - **Zero admin:** managed pod hosts like PikaPods (from $3.70/mo, prepaid balance) or InstaPods ($7/mo flat for 2 vCPU and 2 GB) — HTTPS, backups and the webhook URL come configured out of the box.
> - **Official and predictable:** n8n Cloud Starter, €20/mo billed annually, capped at 2,500 executions a month.
> - **Lowest long-run cost, you're the admin:** RackNerd's annual KVM VPS plans — a 2.5 GB tier lands around $26 a year, meaning one year of RackNerd costs less than four months of a $7 managed plan. 👉 [see RackNerd's current VPS plans and annual prices](https://bit.ly/my-rn)

## What n8n actually needs from a host

Five things, and only one of them is the server itself.

1. **It stays on.** Scheduled workflows only fire while n8n is running. Serverless platforms that idle-to-sleep will silently drop your triggers — Render's free tier, for example, spins down after 15 minutes without inbound traffic and takes about a minute to wake back up.
2. **Webhooks can reach it.** External services call n8n on a public HTTPS address, not a bare IP. Behind a reverse proxy, n8n's default webhook address won't work until you set it by hand.
3. **Credentials survive a rebuild.** n8n generates a random encryption key on first launch and saves it in the `~/.n8n` folder. Lose that folder, and every stored API key becomes unreadable.
4. **Backups exist, and you can restore them.** A failed upgrade should cost you an hour, not your workflow library.
5. **Nothing meters your executions.** This is the quiet argument for self-hosting: on n8n Cloud Starter, runs past 2,500 a month fail immediately with an error. On your own server, the only limit is the hardware.

Hardware-wise, n8n boots in 512 MB, but that's below reliable production use. Under 1 GB you'll hit out-of-memory crashes once workflows process large payloads, and around 2 GB is the practical floor. The default SQLite database is fine for testing; production wants PostgreSQL on storage that survives restarts. And if you plan to use community nodes, you need a Docker-based deployment with `N8N_COMMUNITY_PACKAGES_ENABLED=true` set.

## RackNerd's plans: the lineup, and which tiers to skip for n8n

RackNerd is a budget KVM VPS provider — unmanaged, full root access, standard Linux images, data centers across the US including Los Angeles, San Jose, Dallas, Chicago and New York. There's no one-click n8n template here; you install it yourself via Docker, which is exactly why the pricing sits where it does. The value is in annual billing, and the regular KVM lineup looks like this:

| Plan | RAM | vCPU | SSD | Annual price | Order |
| --- | --- | --- | --- | --- | --- |
| 768 MB | 768 MB | 1 | 15 GB | $10.99/yr | [ Order this plan](https://bit.ly/my-rn) |
| 1.5 GB | 1.5 GB | 1 | 25 GB | $16.99/yr | [ Order this plan](https://bit.ly/my-rn) |
| 2.5 GB | 2.5 GB | 2 | 40 GB | $25.99/yr | [ Order this plan](https://bit.ly/my-rn) |
| 4 GB | 4 GB | 3 | 60 GB | $38.99/yr | [ Order this plan](https://bit.ly/my-rn) |
| 6 GB | 6 GB | 4 | 80 GB | $56.99/yr | [ Order this plan](https://bit.ly/my-rn) |
| 8 GB | 8 GB | 4 | 100 GB | $74.99/yr | [ Order this plan](https://bit.ly/my-rn) |
| 12 GB | 12 GB | 6 | 150 GB | $109.99/yr | [ Order this plan](https://bit.ly/my-rn) |
| 16 GB | 16 GB | 8 | 200 GB | $139.99/yr | [ Order this plan](https://bit.ly/my-rn) |

Monthly billing is available at higher rates, bandwidth allowances run into the terabytes, and RackNerd rotates promo deals through the year that occasionally undercut these regular rates — the order page always shows the live price. The annual rate you sign up at is the rate that renews, which matters more than most people realize (more on that below).

For n8n specifically, the mapping is straightforward:

- **Skip the 768 MB and 1.5 GB tiers for n8n.** They're fine little servers for other projects, but they sit below n8n's 2 GB practical floor. Buying cheap and then fighting out-of-memory crashes is a worse deal than buying the right tier once.
- **2.5 GB is the sweet spot** for a solo user or a small team running dozens of workflows, with room for PostgreSQL alongside n8n.
- **4 GB if your workflows move large payloads**, process files, or several people are in the editor at once.
- **8 GB and up makes sense** once you activate queue mode for better performance under heavy load, or start bundling other self-hosted tools on the same box.

Because this is KVM virtualization with full root, Docker runs properly and updates are just a `docker pull` away. What you don't get is anyone doing it for you — backups, SSL, security hardening and n8n updates are all your job. If that sounds like a feature rather than a chore, 👉 [check RackNerd's current annual pricing](https://bit.ly/my-rn) and pick your tier from there.

## What n8n hosting really costs by year two

Sticker prices lie, and they lie in two specific ways: intro pricing and prepay traps. Running the actual year-one and year-two numbers changes the ranking of almost every host.

Take Hostinger's KVM 1 at $6.49 a month. That price requires paying 24 months upfront — $155.76 before you've run a single workflow — and it renews at $11.99 a month. IONOS pre-installs n8n for you and includes a 30-day money-back guarantee, but its VPS M+ headline of $5 a month only lasts three months before jumping to $14, which makes the real first-year bill about $141. DigitalOcean charges $24 a month for a 2 vCPU, 4 GB machine, or $288 a year.

Against that, the annual-billing crowd looks different. Hetzner's CAX11 comes to roughly €39 a year for 2 vCPU and 4 GB — the best verified price-per-RAM in the DIY market, though only in European data centers. A 2.5 GB RackNerd plan at $25.99 a year works out to about $2.17 a month, and the same rate renews in years two and three. Over three years you'd spend around $78 — less than four months of n8n Cloud Starter, which bills €20 a month on an annual commitment and caps you at 2,500 executions.

The trade you're making is time and responsibility. Someone paying $7 a month for a managed pod is buying back the evening they'd otherwise spend configuring a reverse proxy. 👉 [compare RackNerd's plans against the managed options above](https://bit.ly/my-rn) with that exchange rate in mind — if your hourly rate is anything near a freelancer's, the managed premium buys itself back quickly.

## When a budget VPS is the wrong answer

If you've never administered a Linux server and have no desire to learn, DIY hosting is the wrong tool regardless of price. A first-time setup on a bare VPS — Docker, a reverse proxy, a Let's Encrypt certificate, the n8n container — realistically takes 30 to 60 minutes, and then it's yours to patch and monitor forever. Managed hosting exists precisely to skip that.

The managed landscape, for reference, currently looks like this (information only — check each provider's own site for current terms):

| Option | Price | Type | What stands out |
| --- | --- | --- | --- |
| n8n Cloud (official) | €20/mo billed annually | Managed | 2,500 executions/mo cap; over-cap runs fail immediately |
| OpenHosst | $2.99/mo | Managed | PostgreSQL, daily backups, community nodes enabled |
| PikaPods | From $3.70/mo | Managed | Cheapest verified; prepaid balance, no shell access |
| InstaPods | $7/mo flat | Managed | 2 vCPU, 2 GB; webhook URL and HTTPS preconfigured |
| Hetzner CX23 | €5.49/mo | DIY VPS | 2 vCPU, 4 GB, 40 GB NVMe; German/Finnish locations only; no n8n app |
| DigitalOcean | $12–24/mo | DIY VPS | Well-documented; $12 gets you 2 GB |
| Hostinger KVM 1 | $6.49/mo | DIY VPS | 1-click n8n template; 24-month prepay, renews at $11.99 |
| IONOS VPS M+ | $5/mo intro, then $14 | DIY VPS | n8n pre-installed, EU data centers, 30-day money-back |
| Elestio | From ~$16/mo | Managed | Updates n8n for you automatically |

One pattern worth noticing: the hosts that fail n8n users usually fail on the five checks from earlier, not on specs. Hostinger's free backup is weekly on the cheapest plan and daily backups cost extra. Some managed platforms don't document whether your encryption key survives a rebuild at all. These are the questions to ask before buying anything, including from RackNerd — where the honest answer is that all five checks are yours to handle.

## Your first hour on a new VPS: a setup checklist

The order matters here, because the expensive mistakes happen early.

1. **Point a domain at the server and get HTTPS first.** Install Docker and a reverse proxy, issue a Let's Encrypt certificate, and set the `WEBHOOK_URL` environment variable explicitly — the default webhook address won't work behind a proxy until you do.
2. **Persist the `~/.n8n` folder before doing anything else.** It holds the auto-generated encryption key. Copy that key somewhere safe at first launch; if the folder is lost on a rebuild, every saved credential in n8n becomes unreadable.
3. **Open the right firewall ports.** Direct access uses port 5678, while the reverse proxy route needs 80 and 443. Then lock down everything else.
4. **Switch from SQLite to PostgreSQL** if this instance matters to you. It's the difference between a fragile test setup and a production one.
5. **Set up backups and test a restore once.** n8n's state is essentially a database plus that `.n8n` folder, so a nightly dump to storage outside the server is cheap insurance.
6. **Enable community packages** with `N8N_COMMUNITY_PACKAGES_ENABLED=true` if you use them — the official Docker image handles updates cleanly after that.

## Questions people ask before choosing n8n hosting

**Can n8n run on the cheapest VPS plan?**
It can boot in 512 MB, but that's below reliable production use, and under 1 GB you'll hit out-of-memory crashes with real workloads. Treat 2 GB as the practical floor, which on RackNerd's ladder means starting at the 2.5 GB tier.

**Is the official n8n Cloud worth it?**
If your organization needs an official vendor, SLA-backed support and automatic updates, and your usage stays under 2,500 executions a month, yes. Past that cap, runs fail with an error, and at €20 a month the math tilts hard toward self-hosting.

**Do I need Docker to self-host n8n?**
The official Docker image is the cleanest way to install and update n8n, and community nodes require a Docker-based deployment. Ordinary website shared hosting can't run n8n at all — it's a Node.js service that needs a persistent, always-on process.

**What does a year of n8n hosting actually cost?**
Rough anchors: about $44 for PikaPods, €39 for Hetzner's CAX11, $84 for InstaPods, $144 for DigitalOcean's 2 GB droplet, $26 for a 2.5 GB RackNerd annual plan, and €240 for n8n Cloud Starter.

## Where that leaves you

Never touched a terminal, or don't want to? Pick a managed pod in the $3–7 range and start building workflows the same afternoon. Need an official vendor for compliance reasons? n8n Cloud, knowing the execution cap. Comfortable with Docker and counting costs over years? That's where an annual KVM plan like RackNerd's 2.5 GB tier — around $26 a year, renewing at the same rate — quietly beats everything else, provided you own the five checks yourself. EU-based and just want the cheapest credible hardware? Hetzner's CAX11 remains the reference point.

Before you order anything, confirm three things: the plan has at least 2 GB of RAM, the data center location is one you're comfortable with, and you've accepted that updates and backups are on your calendar now. Then, the moment the server is up, copy that encryption key somewhere safe — everything else is fixable. 👉 [see RackNerd's latest annual VPS deals before you decide](https://bit.ly/my-rn)
