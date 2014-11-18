# [fit] stabilizing
# [fit] the registry

![100%, left](assets/npm.png)


---

![left](assets/bumper2_nasa_big.jpg)
# [fit] C J Silverio
## [fit] devops at npmjs.com
## [fit] @ceejbot

^ I'm going to ignore the web site almost entirely in this presentation because a) I don't have time, and b) it's the most refreshingly boring part of our architecture.

---

# [fit] side project
# [fit] 100% couchdb
# [fit] donated hosting
# [fit] IrisCouch ![inline](./assets/iriscouch.jpg)

![15%, left](assets/npm-logo-white-trans.png)

^ To understand instability, understand where it came from. Side project. Couchdb application. All logic in couchdb, also all data including tarballs. Donated hosting from IrisCouch, cheap back when it was small. Then node took off.

---

![120%](assets/npm_monthly_dls.png)

^ Blue line: package counts. Red line: monthly downloads. Jan 2013 13 mill per month. We now get 17 million per day. 300 mill / month. In Nov 2013, we hit. 116M / month, 46K packages.

---

![original](assets/servers_down.jpg)

^ Benign neglect caught up. This is when Nodejitsu ran the "scale npm" fundraiser. Your money went to keeping it hosted at all during this time.
---

![150%, left](assets/arch_dec_2013.png)

# [fit] December 2013

^ Step one: put a CDN in front of everything. stop serving the tarballs from CouchDB. Put a CDN in front of everything.

---

![left](assets/arch_jan_2014.png)

# [fit] January 2014

^ This was more or less holding up under the load, though there were still outages with absolutely no visibility into why.

---

# ![inline](assets/npm.png) February 2014

* company founded & funded
* 100% hosted on Joyent
* several skimdbs load-balanced by Fastly
* hand-built CouchDB + Spidermonkey
* automation by bash
* Twitter tells us when we're down

^ The registry is no longer in emergency mode, but it's still suffering periodic downtime. We have a contractor in South Africa, Jacques Marneweck, who is doing fantastic work getting us functional on SmartOS.

---

# This is when I arrive.

(funding means you can hire!)

* PagerDuty account: first thing I did
* Nagios all hooked up & monitoring basic host health
* we have maybe 10 hosts total driving the registry

---

# [fit] Funding also means attention
# [fit] from bounty-hunters.

^ We had security breaches my first week on the job, from "white hats" in search of bounties. Next major outage. We replaced the registry cert but didn't choose one from a provider that old clients knew how to trust. There are a LOT of old clients out there, some of them un-upgradeable.

---

# [fit] security audit
![50%, left](assets/lift-security.png)

^ Excellent firm, lots of work fell out of this. Raquel Velez, our web engineer, got most of the work resulting from this, but we had to start firewalling.

---

# Stabilization stage 1
## reactive

* monitor everything more deeply
* methodically identify & monitor causes of outages
* react quickly to fix problems
* Twitter is no longer telling us when we're down

---

# Stabilization stage 2
## proactive

* our second devops person: Ben Coe
* recurring problems fixed in the apps
* monitoring checks self-heal
* redundancy everywhere
* automation!
* our night shift is bored!

---

![fit, left](assets/arch_jul_2014.png)
# [fit] June 2013
# [fit] Superficially
# [fit] similar.

^ Last month. Pretty reliable. We know when our providers are down before they do sometimes. Manta is gone-- tarballs are served from a file system behind nginx. We have a write primary & a second replication primary, and a bunch of leaves that just read from the replication primary.

---

# major changes

100% on AWS
Ubuntu Trusty
70/30 split between us-west-2 & us-east-1
100% automated with ansible
52 running instances, variable

^ DJ mode: The new infrastructure would run alongside the old one. I'd fade the old one out & the new one in & registry users wouldn't notice the transition.

---

# the stack

* Fastly CDN  for Varnish cache & geolocality
* nginx to serve static files
* pound to terminate TLS
* CouchDB for package metadata & app logic
* nagios + PagerDuty for monitoring
* InfluxDB + Grafana for metrics
* Tarsnap for backups

^ We pay to have InfluxDB hosted.

---

![](assets/one_dashboard.png)

^ Very happy with InfluxDB + Grafana combo.

---

![left](assets/ansible_run.png)
![right](assets/ansible.png)

^ Very happy with ansible: hosts are configured without being touched by human hands.

---

# weak points

* single points of failure: Fastly, write primary
* still looking for an off-AWS backup
* expensive to run: too many couchdbs
* too entangled with couchdb
* complex in odd places: the skimworker, for example

---

# [fit] I now praise
# [fit] CouchDB

^ Carried the registry a long way before falling over. Replication is rock-solid building block for architecture. Reliable changes feed that is crucial to how we build our redundancy. Doesn't fall over now that we don't make it do things its bad at.

---

# [fit] my next goal:
# [fit] make it cheap

^ This is already in progress. In fact, the diagram I showed you of how the registry works today is how it worked on Sunday. It's different already, and I very much hope to talk to you all again in three months to tell you how I got us serving the registry on one dollar a day.

---

![left, fit](assets/registry_arch_next.png)

# [fit] by next week
# [fit] haproxy
# [fit] 50-50 region balance
# [fit] cheaper by far

---

# [fit] my long-term goal:
# [fit] npm as reliable
# [fit] utility
