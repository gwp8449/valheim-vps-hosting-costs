# Valheim server hosting: the specs that actually matter, what it really costs, and how to run your own world on a high-clock VPS

Most groups hit the same wall about forty hours into a Valheim run. The world lives on whoever started it, and that person becomes the load-bearing pillar of everyone's weekend. If they're at work, nobody plays. If their PC reboots mid-raid, the base is gone. Somebody eventually types "valheim server hosting" into a search bar and starts the slow process of finding out that the free options are more work than they look and the paid ones vary wildly in what you actually get.

There are really three paths: run the dedicated server on your own hardware, rent a managed game host with a Valheim button in the panel, or take a plain VPS and build it yourself. The third option is the cheapest for the hardware you get, and it's where RainYun fits — a Chinese cloud provider that has been unusual in stacking high-clock desktop CPUs into its game-server product line. This article covers what Valheim needs, what RainYun's plans actually cost, and where the seams are.

## What Valheim actually asks of a server

Valheim's dedicated server isn't a heavyweight process. The official documentation puts the floor at a 4-core CPU with 2 GB of RAM and 2 GB of storage. That RAM figure is optimistic. Multiple community Docker images recommend "over 4 GB for stable operation," and the Arch Wiki notes that even a small world of about 25 MB with a handful of players pushes the process to roughly 3 GB in practice. Tencent Cloud's own VPS guide splits it by group size: 1–5 players on 4 GB, 5–10 on 6 GB, 10+ on 8 GB.

| Players | RAM | CPU | Storage |
| --- | --- | --- | --- |
| 1–5 | 4 GB | 2 vCPU, fast single core | 5 GB |
| 5–10 | 6–8 GB | 3–4 cores, 3.5 GHz+ | 5–10 GB |
| 10+ or heavily modded | 8–16 GB | 4–6 cores, high clock | 10 GB+ |

The number that gets overlooked is clock speed. Valheim's world simulation is largely single-threaded — raids, AI pathing, physics and the save routine all queue up on one core. A 16-core older Xeon running at 2.2 GHz will feel worse than a 4-core chip at 5 GHz, and that's not a subtle difference: it's the one that shows up as the stutter when a troll raid spawns and three people are inside the same base. If you're comparing hosts on price alone, you're comparing the wrong axis first.

Three setup details worth knowing before you pay anyone:

- Valheim uses two consecutive UDP ports. Default is 2456 and 2457; some setups also open 2458. If they're closed, the server runs fine and nobody can join.
- The server password must be at least 5 characters **and** must not appear inside the world name. Name your world `Midgard` and set the password to `gard` and the server will refuse to start — the error shows up in the log, not in a control panel.
- Crossplay (`-crossplay`, which routes through PlayFab) lets Steam, Microsoft Store and Xbox players share a world, but it forces you to stay vanilla. Xbox clients can't load gameplay mods.

## Why the "cheap tier" trap gets so many people

Advertised starting prices are almost always the 2 GB tier. For Valheim, 2 GB is a testing box, not a server. Groups buy the $5 tier, find that saves take longer and stutter when the world gets dense, then upgrade two weeks later and pay the migration cost in downtime.

The other trap is per-slot pricing on managed hosts. You pick 10 slots, you pay for 10 slots, whether or not anyone logs in during a busy month. If your play pattern is "three of us on Saturday and Sunday, nothing Monday through Friday," that's a rough deal.

That's the gap RainYun's billing model is aimed at, and it's worth understanding before looking at the price table.

## Where RainYun fits

RainYun (雨云) has been operating since 2018 and runs its own management panel, with ISP, IDC and ICP licensing in China. Alongside general cloud servers, bare metal and object storage, it sells a **game cloud** line — VPS and panel instances built for Minecraft-style workloads, which in practice means the same thing Valheim wants: high single-core clock speed, DDR4 memory and NVMe storage.

The CPU roster is the interesting part. It includes Intel i9-14900KF and i9-13900KF instances where the efficiency cores are disabled, so your instance never gets scheduled onto a slow core — the listings describe water cooling and an overclock that some sources put as high as 6.3 GHz. Below that sit Ryzen 9950X, Ryzen 7950X and Ryzen 5900X machines, plus value tiers on Xeon Gold 6146 and E5-2666v3, and EPYC 7A23/7702 options.

👉 [Compare RainYun's current game cloud plans and pricing](https://www.rainyun.com/MzA2MTc2_)

A note on scope: plenty of Chinese hosting reviews describe these machines as "the best choice for Minecraft server hosting," and the same characteristics apply to Valheim, but you won't find a RainYun marketing page with a Valheim logo on it. There's no one-click Valheim template here. You get root access and you install it.

## RainYun game cloud plans and prices

The table below is RainYun's published fixed-billing reference lineup for the game cloud VPS product, one config per CPU model. Actual order pricing varies by region, CPU model, IP type, disk and bandwidth, so treat these as the entry points.

| CPU model | Core / RAM | Upload bandwidth | System + data disk | Price | Order |
| --- | --- | --- | --- | --- | --- |
| E5-2666v3 | 2 vCPU / 4 GB | 10 Mbps | 30 GB + 20 GB | ¥65/month | [Pick this tier](https://www.rainyun.com/MzA2MTc2_) |
| Xeon Gold 6146 | 2 vCPU / 4 GB | 10 Mbps | 30 GB + 20 GB | ¥80/month | [Pick this tier](https://www.rainyun.com/MzA2MTc2_) |
| Ryzen 9 5900X | 1 vCPU / 2 GB | 5 Mbps | 30 GB + 20 GB | ¥125/month | [Pick this tier](https://www.rainyun.com/MzA2MTc2_) |
| Ryzen 9 7950X | 1 vCPU / 2 GB | 5 Mbps | 30 GB + 20 GB | ¥139/month | [Pick this tier](https://www.rainyun.com/MzA2MTc2_) |
| i9-13900KF | 1 vCPU / 2 GB | 5 Mbps | 30 GB + 20 GB | ¥145/month | [Pick this tier](https://www.rainyun.com/MzA2MTc2_) |
| i9-14900KF | 1 vCPU / 2 GB | 5 Mbps | 30 GB + 20 GB | ¥145/month | [Pick this tier](https://www.rainyun.com/MzA2MTc2_) |

Those are the *smallest* config in each CPU tier, which is why the cheaper Xeon rows sit above the pricier chips in raw cores. Larger configs on the same silicon exist and cost more. Verified examples from published reviews: a 4-core / 8 GB i9-14900K game cloud VPS listed at ¥200/month; the same shape on Ryzen 7950X at ¥153/month and on Xeon Gold 6146 at ¥100/month; an EPYC 7A23 4-core / 8 GB at ¥140/month. Annual prepay takes 30% off across the board.

Devices are prepaid by month or year, and the domestic Chinese nodes (Jiangsu Suqian and Zhenjiang, Hubei Shiyan, Zhejiang Ningbo and Wenzhou) carry DDoS protection in the 100–200 Gbps range depending on node.

## If your group isn't in mainland China

This is the part to think through carefully. The game cloud line lives in mainland Chinese datacenters. If half your group is in Europe or North America, that's the wrong product, no matter how good the CPU is — 200 ms of latency ruins Valheim combat timing far more than 1 GHz of clock speed fixes it.

RainYun's general cloud server line covers overseas regions instead: Los Angeles, Tokyo and Hong Kong, plus Chongqing, Shenzhen and others domestically. Those nodes run on AMD EPYC silicon rather than the overclocked desktop chips, and have lower bandwidth caps, but the network path is the point.

**US (Los Angeles) — unlimited traffic, AMD EPYC**

| Plan | Core / RAM | Up / down | Price |
| --- | --- | --- | --- |
| KVM Entry | 1 vCPU / 1 GB | 20 / 30 Mbps | ¥25/month |
| KVM Standard | 2 vCPU / 2 GB | 30 / 50 Mbps | ¥34/month |
| KVM Mid | 2 vCPU / 4 GB | 40 / 60 Mbps | ¥44/month |
| KVM Advanced | 4 vCPU / 4 GB | 60 / 80 Mbps | ¥60/month |
| KVM High | 4 vCPU / 8 GB | 80 / 100 Mbps | ¥113/month |
| KVM Flagship | 8 vCPU / 16 GB | 100 / 150 Mbps | ¥197/month |

**Hong Kong — traffic-accumulating, Xeon Gold**

| Plan | Core / RAM | Up / down | Price |
| --- | --- | --- | --- |
| KVM Entry | 1 vCPU / 1 GB | 10 / 10 Mbps | ¥28/month |
| KVM Standard | 2 vCPU / 2 GB | 20 / 20 Mbps | ¥35/month |
| KVM Mid | 2 vCPU / 4 GB | 20 / 20 Mbps | ¥48/month |
| KVM High | 4 vCPU / 4 GB | 30 / 30 Mbps | ¥68/month |
| KVM Top | 4 vCPU / 8 GB | 30 / 30 Mbps | ¥108/month |
| KVM Flagship | 8 vCPU / 16 GB | 50 / 50 Mbps | ¥188/month |

Disk is elastic on both, configurable from 30 GB up to 2000 GB depending on plan. For Valheim specifically, the numbers that matter are RAM and the *upload* column — the server sends the world state down to every player, so upload is your real ceiling. At roughly 50 Kbps per active player, even 20 Mbps upload is far more than a 10-player group will consume.

👉 [See the overseas node lineup and live pricing](https://www.rainyun.com/MzA2MTc2_)

## Fixed billing vs dynamic billing, in plain terms

RainYun's game cloud runs two billing models, and picking the wrong one is the most common way to overpay.

**Fixed billing** is a normal monthly rental. You pay the listed price, the instance runs, nothing else accrues.

**Dynamic billing** charges a lower base monthly rent and then bills daily for CPU time above a 20% utilization baseline. The unit is a virtual "CPU electricity" figure — memory and disk don't generate it. Below 20% CPU, you burn nothing extra.

The reasoning behind it is honest: Valheim's server sits near idle when nobody's online, and only works its single fast core when people are actually playing. A group that plays four hours a day will spend less on dynamic billing than on fixed; a group that keeps a server up around the clock with a rotating cast of players will usually find that fixed billing comes out ahead, because CPU time above the baseline accrues daily for a month. The rough rule published in Chinese RainYun guides is: more than about two hours of active play per day, take fixed.

## Which plan to actually buy

| Your group | Recommended shape | Where | Rough monthly cost |
| --- | --- | --- | --- |
| 2–4 friends, vanilla, evenings only | 2 vCPU / 4 GB, dynamic billing | Suqian or Shiyan game cloud | ¥65–80 |
| 5–10 friends, some mods, most days | 4 vCPU / 8 GB, fixed billing | i9-14900K, Ryzen 7950X, or 7A23 node | ¥100–200 |
| 10+ players, heavy builds, long-term world | 4–8 vCPU / 16 GB, fixed billing | Zhenjiang or Wenzhou i9 node | ¥200+ |
| Mixed EU / NA / Asia group | 4 vCPU / 8 GB | US Los Angeles or Hong Kong cloud server | ¥113 or ¥108 |

Two honest observations. First, a 2 vCPU / 4 GB box on Gold 6146 at ¥80/month will run a vanilla world for five people comfortably — buying the i9 tier before you need it is spending money on a problem you don't have yet. Second, if your players are spread across continents, put the server in Los Angeles and accept slightly higher latency for the Asian players, rather than putting it in Zhejiang and giving the Europeans a 250 ms handicap.

## The limits worth knowing before you sign up

- **The panel is Chinese.** The management interface, docs and ticket system are in Simplified Chinese. If you don't read Chinese, budget some time with a translation tool.
- **Payment is Alipay and WeChat Pay.** No card checkout is documented.
- **Prices are in CNY**, so your actual cost moves with the exchange rate and any foreign transaction fee your bank adds.
- **This is a VPS, not a managed Valheim host.** You handle SteamCMD updates, backups, mod installation and version sync after every Valheim patch. If you want someone else to do that, a managed game host is the right purchase and this isn't.
- **Compliance applies to domestic nodes.** Public, commercial or large-scale Chinese game servers come with filing obligations that a hosting plan doesn't solve for you.

## Getting the server running

The install on a RainYun Linux instance is the standard Valheim dedicated server process, and it's genuinely about twenty minutes of work.

1. Order a game cloud VPS or cloud server, pick a Linux image (Debian or Ubuntu both work), and note the public IP and port mapping from the panel.
2. Install SteamCMD, then pull the dedicated server: `steamcmd +login anonymous +app_update 896660 validate +quit`.
3. Create the launch script with `-name`, `-world`, `-port 2456`, `-password` and `-public 1`. Keep the password at 5+ characters and out of the world name.
4. Open UDP 2456 and 2457 in the instance firewall, and use the panel's port mapping if your plan uses NAT rather than a dedicated public IP.
5. Run it as a `systemd` service, not inside a terminal session, or it dies the moment your SSH window closes.
6. Connect from the client with **Add Server** using the IP and port, or via the crossplay join code if you enabled it.

If you take the panel variant instead of the plain VPS, the environment is preconfigured and you skip most of step 2 — but the panel is built around Minecraft server images, so keep expectations calibrated and check what images are actually available before ordering.

👉 [Start with a one-day trial and test it before you commit](https://www.rainyun.com/MzA2MTc2_)

## Cost, risk and the refund policy

Two policies make testing cheap. Cloud server and game cloud products can be trialled for ¥1 for one day, once per product type — higher-spec machines cost a little more for the trial day. And there's a 7-day no-reason refund policy, available up to three times per account, with self-service cancellation during the window.

That combination is more generous than most hosts, and it changes the risk calculation. Run the trial day with your actual world save loaded and your actual group connected, watch the CPU graph during a raid, and then decide between fixed and dynamic billing with real numbers instead of a guess. Retention policy matters too: on the panel product, data is kept for 30 days after the machine is switched off.

## FAQ

**Can RainYun run a modded Valheim server?**
Yes, within limits. BepInEx and client-side mods work on a VPS you control, but every Steam client has to match the server's mod versions or joins fail. On the panel product you're restricted to supported images, and crossplay forces vanilla anyway.

**Is 2 GB RAM enough?**
For a test world, briefly. For a world you care about, no. Plan for 4 GB and size up to 6–8 GB if you're running mods or a dense base.

**Do I get a dedicated IP?**
Some Chinese game cloud plans are NAT-based and share a public IP, with port mappings — usually 15 forwarded ports, which is enough for Valheim's 2456/2457 pair. Dedicated high-defense IPs can be added for an extra monthly fee. Overseas cloud server plans come with their own IP.

**What if Valheim patches and breaks the server?**
That's on you: re-run the SteamCMD update, restart the service, and restore a save if the world got migrated. Container images that handle auto-updates and scheduled world backups exist and are worth setting up on day one, because the wait for someone to fix a corrupted save on a Sunday evening is measured in hours.

**Will my friends in Europe get a playable connection on a Chinese node?**
Realistically no. Cross-border routing to mainland China nodes means high and variable latency. Use the Los Angeles or Hong Kong cloud servers for a geographically mixed group.

## The short version

Valheim needs less CPU than people assume and more RAM than the official floor suggests, and it strongly favours clock speed over core count. If your group sits in or near China and plays in bursts, RainYun's game cloud line is priced well for that pattern — a 2 vCPU / 4 GB Gold 6146 instance at ¥80/month runs a vanilla five-person world, and dynamic billing means quiet weeks cost less. If you're spread across continents, take the Los Angeles or Hong Kong cloud server instead and accept the lower clock speed in exchange for a usable ping. Either way, run the ¥1 trial day with your real world loaded first; a single day of actual usage tells you more than any spec table will.
