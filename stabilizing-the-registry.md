# [fit] stabilizing the
# [fit] registry

![100%, left](assets/npm.png)

---

![left](assets/bumper2_nasa_big.jpg)
# [fit] C J Silverio
## [fit] director of engineering
## [fit] @ceejbot

---

# [fit] Roll back to the last
# [fit] Node Summit, Dec 2013

---

![original](assets/servers_down.jpg)

# October 2013

^ I was running a team writing node services & wondering how I could protect myself from npm outages. Thought I had to run a registry; that was too much trouble.

---

# [fit] Feb 2013:
# [fit] hired into a brand new company
# [fit] to put out the fire

^ Not just run a registry, but the registry. Ulp.

----

# [fit] scaling problem
# [fit] appearing as a
# [fit] stability problem

---

# [fit] "scaling"
# [fit] capacity to meet
# [fit] growing demands

^ Definitions. Whatever form those demands take

---

# [fit] "At scale"
# [fit] huge demand & lots of data

^ Google, Facebook, Apple, Twitter-- those are "at scale". Peeps in audience from Netflix & Paypal have a bigger scaling challenge. Npm: not at scale. But when hundreds of cos start using it, it acquires a scaling problem.

---

# [fit] "stability"
# [fit] not falling over
# [fit] under normal demand

^ For bonus points, not falling over under peak demand.

---

# [fit] 129,796 packages
# [fit] 239 GB package tarballs
# [fit] 40 million package dls/day
# [fit] 1500 req/sec, peak 3000

^ If I gave you those requirements as a greenfield project, you wouldn't have much trouble building a system that met them. The problem is that you never have a greenfield proj. You have legacy projects.

---

# [fit] "Legacy"
# [fit] Anything you've put into production

^ Legacy is inertia. It's mass that slows down change. Code in production can't be changed without understanding how the existing system works.

---

# [fit] Jacques Marneweck
# [fit] Benjamin Coe
# [fit] Laurie Voss

^ The team. I did none of this stuff alone. Jacques for SmartOS expertise. Ben is my comrade in dev ops arms. Laurie is our CTO.

---

# [fit] January 2013
# [fit] 20K packages
# [fit] .5 million dls/day

^ The registry lives entirely inside a single CouchDB instance. Tarballs are base64-encoded attachments on package documents. The app logic is entirely implemented as javascript inside Couchdb. Ironically, there is 0 node in the registry. (Act 1. Our protagonist goes through her normal day.)

---

# [fit] January 2014
# [fit] 60K packages
# [fit] 8 million dls/day

^ 3x the packages, 16x the dls. Act 2 begins. This is just past where it halted and caught fire, and our protagonist's life is changed forever.

---

Diagram of the architecture, showing one box with couchdb.

---

# [fit] cache
# [fit] rules everything
# [fit] around me

^ Put it behind a CDN. Data not changing? CACHE IT. (Changed the npm application so more of the data is immutable, e.g., you can't change a version once you've published it. We can cache even more!) CDN benefits.

---

# [fit] step 1: CDN
# [fit] Put Fastly.com
# [fit] in front of the registry

^ Obvious? Yes! This put out the worst of the fires. The problem is that it costs $ to do this, and side projects run on donated hosting don't have $.

---

# [fit] don't make your
# [fit] poor database
# [fit] do things it's bad at

^ Binary blobs don't belong in dbs that need to base64 encode them.

---

# [fit] step 2: tarballs
# [fit] get them out of couchdb

^ Out of couchdb & into Joyent's Manta, an object store backed by postgres with a nice API. They're now sitting on a filesystem served by nginx. 1 aws instance is more than capable of handling what load gets through from Fastly, but we have several for region redundancy. Node does the work of pulling tarballs out.

----

![fit](assets/arch_jan_2014.png)

^ January 2014. This was more or less holding up under the load, though there were still outages with absolutely no visibility into why.

---

# [fit] ![inline](assets/npm.png) February 2014
# [fit] company founded

^ It takes money to pay for the CDN & for dedicated engineering working on the rearchitecture.

---

# [fit] hosted on Joyent/SmartOS
# [fit] hand-built CouchDB + Spidermonkey
# [fit] bash scripts to deploy

^ Everything was hand-built. 10 special snowflake servers. This is when I arrive. PagerDuty account: first thing I did. Nagios all hooked up & monitoring basic host health. 10 hosts total.

---

# [fit] step 3: monitoring
# [fit] PagerDuty tells us about problems
# [fit] Not Twitter.

^ Nagios is a horror but it's state of the art, apparently, and it is solid enough to monitor your other monitoring systems.

---

# [fit] Monitoring
# [fit] & alerts

^  is a host up/down is a boring question. you need to monitor your user's experience. is the service working from their POV? if not, what are the telltales?

---

## [fit] reactive monitor
## [fit] monitor deeply
## [fit] fix things quickly

^ Stabilization stage 1: react quickly. Notice when we're down & fix by hand.

---

## [fit] proactive monitoring
## [fit] self-healing
## [fit] \(also things don't break)

^ Monitor checks also fix problems where possible. Deeper problems fixed with engineering. Unit tests for your deployment.

---

# [fit] visibility is a prerequisite
# [fit] but not a solution

^ We're still not actually scaling. CouchDB is still falling over.

---

# [fit] step 4: redundancy
# [fit] several couchdbs for reads
# [fit] 1 for writes, 1 for public replication

^ Separate writes from reads. Separate out replication.

---

# []

# [fit] General lesson #3
# [fit] Add monitoring after
# [fit] every outage

^ What would have tipped you off that things were about to break?

---

![fit, left](assets/arch_jul_2014.png)
# [fit] June 2014
# [fit] Superficially
# [fit] similar.

^ Pretty reliable. We know when our providers are down before they do sometimes. Manta is gone-- tarballs are served from a file system behind nginx. Writes are separated from reads. Scaled by throwing hardware & $ at the problem.

---

# [fit] AWS / Ubuntu
# [fit] 70/30 west/east split
# [fit] 52 running instances, variable

^ Another big change: we moved to Ubuntu on AWS with significant wins in compatibility & tooling.

---

![fit](assets/registry_arch_nov2014.png)

^ The registry today. WAY fewer instances & cheaper to run. Extra capacity for redundancy: if an AWS region goes down, we're fine.

---

# [fit] 50/50 AWS region split
# [fit] haproxy to load balance
# [fit] no AWS-specific magic

^ We use as few AWS features as possible to keep our options open.

---

# [fit] Fastly: geoloc + cache
# [fit] haproxy / CouchDB
# [fit] nginx + a filesystem

^ Simple pieces with good visibility.

---

# [fit] behind the scenes
# [fit] ansible / nagios
# [fit] InfluxDB+Grafana

^ Grown-up ops habits.

---

# [fit] General lesson #4
# [fit] metrics
# [fit] for everything

^ All of your services & instances should report metrics that can be visualized.

---

## [fit] memory & cpu use
## [fit] request latency
## [fit] event counts

^ Process restarts; bytes in & out of load balancers.

---

# [fit] metrics ==
# [fit] visibility

^ What is your system doing? What does it look like on a normal day? What are its trends? We saved $20K on our networking bandwidth bill by monitoring what our haproxies were doing & choking off a healthcheck gone mad.

---

# [fit] metrics drive
# [fit] monitoring

^ This is the goal for me; not there yet.

---

# [fit] General lesson #5
# [fit] automate

^ Chef, Puppet, Salt, Ansible: USE SOMETHING.

---

# [fit] no special snowflakes
# [fit] every instance can be replaced

^ All hosts are configurable remotely & can be replaced easily. Discipline. AWS reboot apocalypse was boring for us.

---

# [fit] General lesson #6
# [fit] the goal is to be
# [fit] BORING

^ Being on-call should be boring. Heroics should NEVER be required. Putting out fires is a bad thing: avoid the fires in the first place.

---

# [fit] if operations are
# [fit] boring
# [fit] you can do the dev

^ replicate into relational dbs, improve searchability, add features: private modules. All of this is work that's possible now that our daily operations are boring.

---

# [fit] Goal: to be the most boring
# [fit] part of your node experience

^ Reliable. Something you don't think about because we're always there.

----

# [fit] npm client <3
# [fit] `npm install -g npm@latest`

^ So many bug fixes & performance improvements.

---

# [fit] npm loves you

^ thanks.