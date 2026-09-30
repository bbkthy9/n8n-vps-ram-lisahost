# n8n VPS: How Much RAM You Actually Need and Which LisaHost Plans Make Sense

Searching for an **n8n VPS** usually leads to the same three questions: how much RAM is enough, whether a cheap VPS will stay stable once workflows get busy, and whether paying for n8n Cloud is simpler than managing the server yourself.

The answer is less complicated than many hosting comparison pages make it sound. n8n can be self-hosted on your own infrastructure, and its current documentation supports Docker, Docker Compose, and several cloud providers. For a production-oriented setup with a database and additional services, n8n specifically points to Docker Compose as the robust deployment path.

LisaHost is one of the hosts that can provide the underlying VPS, but there is an important distinction: **LisaHost does not currently present an n8n-specific managed hosting plan**. Its store is organized around VPS locations, network routes, IP types, bandwidth and traffic allowances. That means the real question is not “Which LisaHost n8n plan should I buy?” but “Which LisaHost VPS configuration makes sense for the n8n stack I want to run?”

That distinction matters because n8n itself is free in its self-hosted Community edition, while the server, storage, database, backups and maintenance are your responsibility.

## What an n8n VPS actually has to handle

An n8n installation is not just a web page sitting on a server. It is a long-running application, and in a serious deployment it normally sits beside a database and sometimes services such as Redis, reverse-proxy software or separate workers.

Current 2026 guides aimed specifically at n8n generally put **4 GB RAM** in the practical starting range for a comfortable single-instance deployment, while 2 GB is described as tighter and 8 GB becomes useful when the stack grows or more services are added.

That does not mean 4 GB is a magical n8n minimum. n8n's own hosting documentation does not define the product using one universal “minimum VPS” number. Instead, its current deployment guidance focuses on the architecture you choose. Docker Compose is explicitly described as suitable for production deployments involving databases and additional services.

For a typical self-hosted setup, I would think about server sizing like this:

| VPS size | Practical use |
| --- | --- |
| 1 GB RAM | Lab, experimentation, very light workloads |
| 2 GB RAM | Small single-instance setups where resources are carefully controlled |
| 4 GB RAM | Sensible baseline for a more comfortable n8n + database deployment |
| 8 GB RAM | More room for Postgres, Redis, heavier workflows, browsers or additional services |
| 16 GB+ RAM | Larger automation stacks, multiple workers or other applications sharing the server |

The important part is that **workflow count alone does not tell you how much memory you need**. Current n8n-focused guidance increasingly points to execution data retention, database choice, queue mode and concurrent workload as the things that actually push resource usage upward.

## Docker matters more than the marketing label

For an n8n VPS, “supports Docker” is much more useful than a host simply advertising a “VPS for developers.”

n8n's current documentation supports Docker and Docker Compose, while its documentation also notes that npm installation becomes deprecated from n8n 3.0 and recommends Docker Compose or the one-line setup instead.

A simple production layout might look like:

text
Internet
   ↓
Reverse proxy / HTTPS
   ↓
n8n
   ↓
PostgreSQL

Optional:
Redis → queue mode / workers
S3-compatible storage → external binary data


The exact design depends on the workload. A personal automation instance can be much simpler than a business setup running many concurrent jobs.

There is also a security detail that is easy to overlook. n8n's security guidance says self-hosters are responsible for putting TLS in front of the instance and handling encryption at rest. In other words, buying a VPS does not automatically turn it into a secure n8n installation.

That means an n8n VPS should really be evaluated on two layers:

1. **The server**: CPU, RAM, disk, network, backups and availability.
2. **The deployment**: Docker, PostgreSQL, HTTPS, credentials, execution retention and updates.

The second layer is where many “cheap VPS” comparisons become misleading.

## LisaHost's current n8n-relevant VPS choices

LisaHost's current store does not show a dedicated n8n package, but its public VPS catalog includes several configurations that can host n8n.

One of the clearest examples is the current **U.S. AS4837 VPS family**. The live product page lists six configurations, all using KVM virtualization and one IPv4 address. The plans range from 1 GB to 8 GB RAM, with monthly prices from ¥68 to ¥998 depending on resources and whether traffic is capped or unlimited.

### Full comparison of the current AS4837 VPS family

| LisaHost plan | CPU | RAM | Storage | Bandwidth | Traffic | Billing | Listed price | Purchase |
| --- | ---: | ---: | ---: | ---: | ---: | --- | ---: | --- |
| 美国4837基础版 | 1 core | 1 GB | 20 GB NVMe | 300 Mbps | 3,000 GB/mo | Monthly | ¥68/mo | [ View plan](https://bit.ly/LIsahost) |
| 美国4837进阶版 | 2 cores | 2 GB | 40 GB NVMe | 500 Mbps | 8,000 GB/mo | Monthly | ¥100/mo | [ View plan](https://bit.ly/LIsahost) |
| 美国4837豪华版 | 4 cores | 4 GB | 80 GB NVMe | 1,000 Mbps | 20,000 GB/mo | Monthly | ¥699/mo | [ View plan](https://bit.ly/LIsahost) |
| 美国4837不限流量Lite | 2 cores | 2 GB | 20 GB NVMe | 200 Mbps | Unlimited | Monthly | ¥398/mo | [ View plan](https://bit.ly/LIsahost) |
| 美国4837不限流量Pro | 8 cores | 8 GB | 80 GB NVMe | 500 Mbps | Unlimited | Monthly | ¥998/mo | [ View plan](https://bit.ly/LIsahost) |
| 美国4837特价年付版 | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 600 GB/mo | Annual | ¥399/yr | [ View annual plan](https://bit.ly/LIsahost) |

The values above are the current public listings on LisaHost's 4837 product page, including its advertised limited-time pricing, bandwidth and traffic allocations.

There is a very obvious dividing line here.

The **1 GB plans are inexpensive, but they do not leave much room for an n8n stack**. The 4 GB and 8 GB options are much closer to the resource range current n8n hosting guides describe as comfortable for self-hosting.

The strange part of this particular catalog is the price jump between the 2 GB and 4 GB plans. The published figures are ¥100/month for 2 GB, then ¥699/month for 4 GB. That makes the product family materially different from the typical low-cost cloud VPS market, where doubling RAM does not usually create anything close to that gap.

So, for n8n, you should look at the LisaHost configuration itself rather than assuming the largest advertised bandwidth number is valuable.

### The 4 GB tier is the meaningful middle ground

The 4-core, 4 GB configuration provides 80 GB of NVMe storage and a 1 Gbps port with 20 TB monthly traffic.

That is technically generous on the network side for a normal automation server. Most n8n users are not going to saturate a 1 Gbps connection simply by running webhook-based workflows, CRM automations, scheduled jobs or API integrations.

The more useful question is what happens inside the machine.

Four gigabytes gives you room for n8n, PostgreSQL and the operating system without forcing every component into a tiny memory budget. If you later add Redis, queue workers or another container, that additional headroom matters more than having hundreds of megabits of unused bandwidth.

The 8 GB unlimited-traffic Pro plan adds even more headroom, but the use case needs to justify it. If the n8n instance is processing normal API automation rather than large binary transfers, the extra network allowance is probably not what determines whether the system performs well.

## LisaHost also sells very cheap annual VPS plans

LisaHost's current low-price annual VPS catalog is much broader. The published page currently includes a number of one-year offers across the United States, Singapore, Taiwan, UK, Japan, Hong Kong, Korea, Vietnam and Germany. Most of these are built around **1 CPU core, 1 GB RAM and 10 GB NVMe storage**.

### Full current annual VPS catalog shown on the public annual-pricing page

| Plan | CPU | RAM | Storage | Bandwidth | Traffic | Price |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| 美国9929精品网 - 非美国原生IP | 1 core | 1 GB | 10 GB SSD | 50 Mbps | 200 GB/mo | ¥199/yr |
| 美国9929精品网 - 美国原生IP | 1 core | 1 GB | 10 GB SSD | 50 Mbps | 400 GB/mo | ¥299/yr |
| 美国4837三网大陆回程优化 - 双ISP家宽住宅美国原生IP | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 600 GB/mo | ¥399/yr |
| 美国纽约双ISP家宽住宅原生VPS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 600 GB/mo | ¥399/yr |
| 美国芝加哥双ISP家宽住宅原生VPS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 600 GB/mo | ¥399/yr |
| 新加坡原生IP VPS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 300 Mbps | 2 TB/mo | ¥466/yr |
| 台湾原生IP VPS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 2 TB/mo | ¥766/yr |
| 英国双ISP住宅IP VPS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 300 Mbps | 2 TB/mo | ¥466/yr |
| 日本原生IP大带宽VPS - 大陆优化网络 | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 600 GB/mo | ¥499/yr |
| 美国9929精品网络 - 双ISP家宽住宅美国原生IP | 1 core | 1 GB | 10 GB NVMe | 50 Mbps | 600 GB/mo | ¥499/yr |
| 香港三网直连CMI/CU2/CN2精品网络ISP VPS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 50 Mbps | 600 GB/mo | ¥566/yr |
| 韩国双ISP家庭宽带静态住宅原生IP VPS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 50 Mbps | 1 TB/mo | ¥699/yr |
| 越南双ISP原生住宅家宽IP VPS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 1 TB/mo | ¥699/yr |
| 香港iCable双ISP原生住宅IP VPS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 1 TB/mo | ¥699/yr |
| 香港HGC双ISP原生住宅IP VPS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 50 Mbps | 600 GB/mo | ¥799/yr |
| 美国住宅宽带静态住宅IP VDS - Astound Broadband | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 1 TB/mo | ¥899/yr |
| 美国住宅宽带静态住宅IP VDS - Seattle Atlas Networks | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 1 TB/mo | ¥899/yr |
| 日本ISP静态住宅IP VDS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 1 TB/mo | ¥899/yr |
| 日本IIJ双ISP原生住宅IP VDS - 特价年付流量版 | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 1 TB/mo | ¥999/yr |
| 德国9929精品网络双栈双原生IP VPS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 600 GB/mo | ¥499/yr |
| 德国双ISP原生住宅IP VDS - 特价年付版 | 1 core | 1 GB | 10 GB NVMe | 100 Mbps | 1 TB/mo | ¥1,099/yr |

The annual catalog currently lists these configurations and prices publicly. Several of them are specifically positioned around native or residential IP characteristics rather than general application hosting.

For an n8n buyer, that distinction is important.

A residential IP can be useful for a workload that genuinely depends on IP reputation or geographic access. It does **not** make n8n itself run faster. For webhook automation, API calls, scheduled jobs and database operations, RAM, CPU, storage and system reliability are normally much more relevant.

That is why the ¥399 annual 1 GB offer can look attractive at first glance but still be a poor fit for a growing n8n stack. The lower price comes with a very small memory budget.

## What about unlimited traffic?

Unlimited traffic sounds attractive until you look at what n8n is actually doing.

If your workflows mostly move JSON payloads between SaaS APIs, send webhooks, update databases, call AI APIs and trigger business processes, traffic may be modest relative to the large allowances advertised on VPS plans.

Unlimited bandwidth becomes more relevant if the same machine is also serving large files, proxying media, storing binary workflow data, running downloads or acting as a broader infrastructure box.

LisaHost currently lists both capped and unlimited-traffic configurations in its U.S. 4837 family. For example, the 2 GB Unlimited Lite plan is listed at ¥398/month with a 200 Mbps port, while the 8 GB Unlimited Pro plan is ¥998/month with 500 Mbps.

For n8n, I would therefore avoid paying a large premium purely for “unlimited traffic” unless the workflow architecture really needs it.

## n8n Cloud versus an n8n VPS

Self-hosting only makes sense when the control you gain is worth taking on the infrastructure work.

n8n's current Cloud pricing lists:

| n8n plan | Current advertised price | Executions | Hosting |
| --- | ---: | ---: | --- |
| Starter | €20/mo, billed annually | 2,500/month | n8n Cloud |
| Pro | €50/mo, billed annually | 10,000/month | n8n Cloud |
| Business | €667/mo, billed annually | 40,000/month | Self-hosted |
| Enterprise | Contact sales | Custom | Cloud or self-hosted |

The current pricing page also states that Cloud plans include unlimited users and workflows, while execution limits depend on the plan. The Business plan is self-hosted only, and the Community Edition remains available as a standard self-hosted version.

There is a subtle but important difference here.

With n8n Cloud, you are paying for the managed environment as well as the software. With a VPS, the server bill can be much lower, but **you become the operations team**.

You have to deal with:

* Docker and container updates
* DNS
* HTTPS/TLS
* database backups
* disk usage
* execution-data retention
* server security
* monitoring
* recovery when something breaks

n8n's own security documentation explicitly places those responsibilities on self-hosters, including TLS and encryption-at-rest considerations.

That is why “VPS = cheaper” is true only in a limited sense. It can reduce hosting cost significantly, but it also transfers maintenance work to you.

## One of the most overlooked n8n costs is disk space

RAM gets most of the attention, but storage can become the silent problem.

Current n8n-focused guides specifically point out that execution data can accumulate over time. A machine that feels comfortably sized on day one can end up with a full disk months later if execution history and binary data are retained without a sensible retention strategy.

That matters even more on LisaHost's annual plans, where many configurations have only **10 GB of storage**.

Ten gigabytes may sound fine for the n8n application itself. It becomes much less comfortable if the same machine stores database growth, logs, Docker layers, backups, binary files and thousands of retained executions.

The 40 GB and 80 GB NVMe configurations in the monthly plans give you much more breathing room.

For a production instance, I would pay more attention to storage capacity than to a giant network number printed in the plan title.

## PostgreSQL, SQLite and queue mode

n8n's self-hosted architecture gives you choices, but the choices have consequences.

For a small experiment, a simple deployment can be perfectly reasonable. As the installation becomes more business-critical, the architecture becomes more important.

Current n8n hosting guidance recommends Docker Compose for robust production deployments, and the official hosting material includes explicit support paths for PostgreSQL, cloud deployment and scaling.

Queue mode is another major dividing line. Current third-party 2026 guidance notes that queue mode introduces Redis and separate worker processes, increasing the memory footprint and making a tiny VPS much less attractive.

So a sensible progression is:

### Small personal automation

A single n8n container with a lightweight database can be enough.

### Business automation

Move toward a larger VPS, use PostgreSQL, enable sensible backups and pay attention to disk growth.

### Higher concurrency

Consider Redis and workers, then size the VPS around concurrency rather than simply counting workflows.

That last point is worth repeating because it is easy to get wrong: having 100 workflows does not automatically mean you need a huge server. One workflow processing large datasets concurrently can create much more pressure than dozens of tiny scheduled jobs.

## Is LisaHost a good fit for every n8n user?

The answer depends heavily on what you actually need from the network.

LisaHost's current catalog is strongly differentiated by network routes, geographic locations and IP types. Its product pages emphasize options such as AS4837, AS9929, CN2 GIA, CMI and residential or ISP-class IP configurations.

That is potentially useful when your automation needs a particular geographic egress point or you have a business reason for a specific IP environment.

For an ordinary n8n server, however, a special residential-IP feature is not automatically an advantage.

The core n8n workload is still:

**application runtime + database + persistent storage + reliable network + secure HTTPS access.**

If those are the requirements, a conventional cloud VPS can be simpler to compare because the pricing is usually centered on CPU, RAM, storage and bandwidth instead of specialized IP characteristics.

Current n8n hosting roundups repeatedly compare providers such as Hostinger, Hetzner, DigitalOcean, Contabo, Kamatera, Linode and others around exactly those dimensions: RAM, deployment method, price, scaling, storage and setup friction.

That does not make those providers automatically preferable. It simply highlights the criterion that matters for n8n: **how much useful infrastructure you get for the actual workload you are running.**

## What current reviews actually say about LisaHost

There is less independent n8n-specific review evidence for LisaHost than there is for larger VPS brands.

Trustpilot currently shows only **one review** for LisaHost, with a displayed TrustScore of 3.2/5. With a sample that small, it is not a meaningful measure of service quality or reliability.

There are several 2026 third-party LisaHost reviews and deal pages discussing its VPS network, IP options and pricing. Some of those sources are affiliate-oriented and some describe personal testing, so they are useful for discovering issues to investigate but should not be treated as a broad customer consensus.

The useful conclusion is not “the reviews are good” or “the reviews are bad.” There simply isn't enough independent review volume to justify a strong claim about customer sentiment.

For an n8n deployment, that makes the provider's concrete product terms more important than a star rating.

## Current LisaHost discounts

LisaHost's published VPS pages already show a mixture of limited-time prices and annual promotional offers. For example, the current AS4837 page lists ¥68/month for the base configuration, ¥100/month for the 2 GB plan, and ¥399/year for its special annual plan.

Several third-party 2026 coupon pages currently list the code `TS-CBP205DQJE` as a 10% LisaHost discount and claim that it can stack with longer billing-cycle discounts. Other third-party pages publish the same code, but I did not find a current official LisaHost page independently confirming the code during this check.

That makes the practical rule simple: **treat the code as a checkout-time possibility, not as a guaranteed price**. Enter it and confirm that the discount is actually reflected before paying.

## How to deploy n8n on a VPS without making the setup fragile

The current n8n documentation offers both a one-line setup and Docker Compose. The one-line installer is designed for quick setup, while Docker Compose is positioned for more robust deployments involving databases and additional services.

For a new VPS, a sensible deployment sequence is:

### 1. Install a supported Linux environment

Keep the operating system lean. The VPS should be an application host rather than a desktop replacement.

### 2. Install Docker and Docker Compose

This gives you a deployment that is easier to reproduce and update than manually managing a Node.js runtime.

### 3. Put HTTPS in front of n8n

n8n's security documentation expects self-hosters to handle TLS through a reverse proxy or equivalent setup.

### 4. Use a persistent volume

Your workflows, credentials and execution data need persistent storage rather than an ephemeral container filesystem.

### 5. Think about the database before the workload grows

A simple deployment can start small, but production architecture deserves more deliberate database planning.

### 6. Set execution-data retention deliberately

Do not assume the disk will take care of itself. Current n8n-focused guidance specifically identifies retained execution data as a common reason storage grows unexpectedly.

### 7. Add monitoring before you need it

Watching RAM, CPU, storage and container health is much easier before an incident than during one.

## Which LisaHost configuration makes sense for n8n?

The current LisaHost catalog gives you a fairly clear resource hierarchy, even though it was not designed specifically around n8n.

For **testing**, the 1 GB annual options are inexpensive, but the limited memory and 10 GB storage leave very little headroom. Current n8n-oriented guides generally treat this class of machine as a lab rather than a comfortable production target.

For a **small, serious deployment**, the 2 GB class is more usable, but still requires disciplined resource management.

For a **production-style single instance**, the 4 GB class is much closer to the current practical guidance for n8n + database, especially when everything is running on the same VPS.

For **higher concurrency or extra services**, 8 GB starts making more sense, particularly if you introduce Redis, queue workers, browser-based automation or other containers.

The trade-off is that LisaHost's current AS4837 pricing becomes dramatically more expensive as you move up the RAM ladder.

That means LisaHost makes the most financial sense for an n8n workload when you specifically value the network, location or IP characteristics of its catalog. If your only requirement is “I need a 4 GB Linux VM for Docker,” the broader VPS market gives you more configurations to compare.

## n8n VPS buying checklist

Before ordering any VPS for n8n, check these numbers rather than getting distracted by the biggest headline feature:

**RAM:** 4 GB is a useful baseline for a comfortable single-instance deployment.

**CPU:** Two to four cores gives more breathing room than a tiny one-core system when workflows become concurrent.

**Storage:** 40 GB is much easier to live with than 10 GB once Docker, PostgreSQL, logs and execution data accumulate.

**Backups:** Confirm whether backups are included, external, manual or entirely your responsibility.

**IPv4:** A public IPv4 address is useful for a conventional webhook setup.

**HTTPS:** Make sure you have a realistic way to expose n8n securely.

**Billing:** Compare promotional pricing with the actual renewal price.

**Traffic:** Don't pay for unlimited bandwidth unless your workflows genuinely need it.

**Geography:** Put the server reasonably close to the systems and users that matter.

**Database:** Decide early whether your architecture should remain simple or move toward PostgreSQL and a more production-oriented stack.

**Maintenance:** Remember that a VPS turns you into the person responsible for patching and recovery.

## The practical bottom line

An **n8n VPS** is fundamentally a resource-sizing problem, not a branding problem.

n8n's current self-hosting documentation supports Docker and Docker Compose, and the Community Edition is available without a paid license. Current 2026 guides generally place 4 GB in the comfortable range for a single self-hosted instance, while 8 GB provides more room for databases, Redis, workers and heavier automation.

LisaHost can provide that infrastructure, but its current product catalog is not built around n8n-specific managed hosting. Its strength is the variety of geographic and network configurations, while its pricing becomes much less straightforward once you move beyond the smallest servers.

For a very small test instance, a low-end annual VPS can be enough to learn n8n. For something that is supposed to run business automation continuously, **1 GB RAM and 10 GB storage are hard constraints rather than bargains**. A 4 GB-class server is a much more realistic starting point, and 8 GB becomes useful once concurrency and supporting services enter the picture.

And before buying anything, compare the total cost of the VPS against n8n Cloud. The current n8n Cloud Starter plan is €20/month when billed annually, while Pro is €50/month on annual billing. Self-hosting can cost less in infrastructure terms, but the difference is that you are now responsible for the machine, database, security, backups and updates.

For LisaHost specifically, the safest way to use the current catalog is to choose the server based on **RAM, storage, CPU and actual network requirements first**, then consider specialized IP or routing features only when your automation workload has a concrete reason to need them.

[👉 View LisaHost's current VPS offers](https://bit.ly/LIsahost)
