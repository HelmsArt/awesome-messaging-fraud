# Awesome Messaging Fraud [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated index of A2P messaging abuse — artificially inflated traffic, SMS pumping, grey
> routes, sender-ID spoofing, smishing and signalling attacks — covering the research, the
> industry guidance, the datasets and the tooling that actually exist.

Industry estimates cited by the Mobile Ecosystem Forum and by messaging providers put
artificially inflated traffic at **5–40% of international A2P SMS traffic in some regions**, with
business losses around **US$1.6 billion in 2023**. Treat both as industry estimates: they are
widely repeated, but the primary research behind them is not public. Despite the scale claimed,
almost everything written about the problem is a vendor blog post or a membership-gated industry
document.

This index exists because there was no single place to start. It deliberately separates the two
vantage points that get conflated — **the message** (is this text a scam?) and **the traffic**
(is this flow fraudulent?) — because the research is overwhelmingly about the first, and the
money is overwhelmingly lost to the second.

**A [gaps section](#gaps--what-does-not-exist) at the end lists what is missing.** That section
is the point of this list; if you are looking for something to build, start there.

## Contents

- [Start here](#start-here)
- [Threat classes](#threat-classes)
- [Industry guidance and standards](#industry-guidance-and-standards)
- [Research](#research)
  - [Traffic-side: AIT, pumping and flow analysis](#traffic-side-ait-pumping-and-flow-analysis)
  - [Message-side: smishing and content classification](#message-side-smishing-and-content-classification)
  - [Adjacent](#adjacent)
- [Datasets](#datasets)
- [Tools](#tools)
- [Gaps — what does not exist](#gaps--what-does-not-exist)
- [Contributing](#contributing)

## Start here

Read in this order if the subject is new to you.

- [SMS pumping, explained](https://www.infobip.com/glossary/sms-pumping) — the shortest correct
  description of the attack. Bots trigger OTP sends to numbers on ranges the fraudster earns
  revenue share from.
- [Artificially inflated traffic: causes and solutions](https://www.infobip.com/blog/artificially-inflated-traffic)
  — how the revenue-share incentive works, which is the part most explanations skip.
- [AIT in A2P messaging: the silent predator](https://mobileecosystemforum.com/2025/01/08/ait-in-a2p-messaging-the-silent-predator/)
  — Mobile Ecosystem Forum. Industry-body framing rather than vendor framing.
- [AIT: the root cause, the solution, and the implication on RCS and network APIs](https://www.ericsson.com/en/reports-and-papers/white-papers/ait-the-root-cause-the-solution-and-the-implication-on-rcs-and-network-apis)
  — Ericsson white paper. The most technically serious of the freely available vendor documents,
  and the only one that addresses what happens as traffic moves to RCS and network APIs.
- [The SMS router in SS7 and SIGTRAN](https://www.p1sec.com/blog/sms-router-in-ss7-and-sigtran-the-quiet-traffic-cop-of-your-messaging-core)
  — where messages are actually steered, which is where the abuse becomes visible.

## Threat classes

Distinct problems that get lumped together as "SMS fraud". They have different victims,
different economics and — the part that matters most here — **different detection surfaces**.

| Class | Layer | What happens | Who pays |
|---|---|---|---|
| **AIT** | Application / business | Traffic inflated artificially to earn revenue share. The umbrella category, not a single technique | The party billed for the traffic |
| ↳ **SMS pumping** | Application / business | The dominant AIT pattern: bots drive OTP or verification sends to attacker-controlled ranges, and the attacker takes a cut of the termination fee | The enterprise sending the OTPs |
| **Grey routes** | Routing / supply chain | Unauthorised or undeclared routing — A2P presented as P2P, local termination, interconnect bypass. Broader than the "A2P disguised as P2P" shorthand | The terminating operator |
| **Sender-ID spoofing** | Routing / content | Forged alphanumeric sender identity, usually to enable phishing | The impersonated brand, and the recipient |
| **Smishing** | Content / user | Phishing delivered by SMS | The recipient |
| **Signalling attacks** | Network / signalling | SS7 or Diameter abuse for interception, location tracking or message manipulation | The subscriber |

**AIT is a category; SMS pumping is its most common implementation.** They are often used
interchangeably, including by vendors, and the conflation obscures that AIT also appears in P2P
and in other revenue-share arrangements.

The layer column is the practical takeaway: these classes intersect in the same ecosystem but
they are not observed with the same data, so a tool built for one rarely transfers to another.

Further reading on the economics and the industry response:

- [SMS vs artificially inflated traffic: fighting fraud and restoring trust](https://www.thefastmode.com/expert-opinion/43869-sms-vs-artificially-inflated-traffic-the-steps-for-fighting-fraud-and-restoring-trust)
- [Artificial inflation of traffic threatens messaging](https://sinch.com/blog/artificial-inflation-traffic-ait-growing-threat-messaging-ecosystem/) — Sinch
- [Artificial inflation of traffic](https://docs.smsportal.com/docs/artificial-inflation-of-traffic) — SMSPortal, written as operational documentation rather than marketing

## Industry guidance and standards

The normative material. Much of it is membership-gated, which is itself part of why the open
literature is thin — noted here so you know what you are missing rather than assuming it does
not exist.

- **GSMA FS.07** — *SS7 and SIGTRAN Network Security.* The protocol-security baseline.
  *Members only.*
- **GSMA FS.11** — *SS7 Interconnect Security Monitoring and Firewall Guidelines*, at v6.0 and
  first approved in 2015. The recommended interconnect filtering rules; the reference commercial
  signalling firewalls implement. *Members only.*
- **GSMA FS.19** — Diameter interconnect security, the 4G counterpart. *Members only.*
- GSMA has formally categorised SMS pumping under Artificial Inflation of Traffic and issued
  carrier guidance.

FS.07 and FS.11 are frequently confused, including in secondary sources — FS.07 covers the
protocols, FS.11 covers monitoring and firewall rules.
- [Mobile Ecosystem Forum](https://mobileecosystemforum.com/) — runs a working group on
  messaging fraud and publishes some material publicly.
- **i3Forum** — carrier-side fraud working group, active on AIT.

## Research

Papers are grouped by vantage point, because the split is the single most useful thing to
understand about this literature.

### Traffic-side: AIT, pumping and flow analysis

Small. This is the traffic-side academic work I have been able to identify in publicly
accessible sources — additions very welcome, and this section is the one most likely to be
incomplete.

- **[Preventing Artificially Inflated SMS Attacks through Large-Scale Traffic Inspection](https://www.usenix.org/conference/usenixsecurity25/presentation/huh)**
  — Huh et al., Samsung Research and Samsung Electronics, USENIX Security '25.
  [PDF](https://www.usenix.org/system/files/usenixsecurity25-huh.pdf).
  **The strongest publicly available traffic-side reference I found.** Characterises AIT
  techniques over 9.4 million SMS request logs and finds attackers rely on short-lived email
  services and reuse common prefixes to generate unverified phone and IMEI numbers at speed.
  Note the vantage point: this is the *service provider* view — request logs before sending —
  not the carrier or aggregator view. The dataset is proprietary and has not been released,
  so the results cannot be independently reproduced or built upon.
- [Private Links, Public Leaks: consequences of frictionless user experience on the security and privacy posture of SMS-delivered URLs](https://arxiv.org/abs/2601.09232)
  — adjacent, but the only work examining what actually happens to the links inside A2P traffic.

### Message-side: smishing and content classification

By contrast, this is an active and well-populated area. Useful when the question is "is this
message a scam", which is not the same question as "is this traffic fraudulent".

- [FraudSMSWalker: benchmarking agentic LLMs for SMS-to-webpage fraud detection](https://arxiv.org/abs/2606.16659)
  — 699 bilingual SMS-to-webpage chains across ten service scenarios, with URLs, hosts and
  reputation metadata deliberately withheld from the model.
- [SpaLLM-Guard: pairing SMS spam detection using open-source and commercial LLMs](https://arxiv.org/abs/2501.04985)
  — evaluates zero-shot, few-shot, fine-tuning and adversarial manipulation across several
  open and commercial models.
- [MOZ-Smishing: a benchmark dataset for detecting mobile money frauds](https://aclanthology.org/2025.africanlp-1.23/)
  — crowd-sourced from Mozambican mobile users, Portuguese and local languages. One of few
  resources outside English.
- [Fine-tuning multilingual language models for low-resource smishing detection using LoRA](https://link.springer.com/chapter/10.1007/978-981-92-0071-9_8)
  — English, Bengali and Swahili; addresses the low-resource-language gap directly.
- [Detection and prevention of smishing attacks](https://arxiv.org/abs/2501.00260) — survey.
- [Machine learning driven smishing detection framework for mobile security](https://arxiv.org/abs/2412.09641)
- [Can you walk me through it? Explainable SMS phishing detection](https://www.usenix.org/system/files/soups2025-wang.pdf)
  — SOUPS '25. The usability side: whether explanations actually help recipients.

### Adjacent

Frequently cited alongside SMS fraud work and useful, but not SMS-specific. Listed separately so
the message-side section is not padded with things that do not belong to it.

- [Fraud-R1: a multi-round benchmark for LLM robustness against fraud and phishing inducement](https://arxiv.org/abs/2502.12904)
  — bilingual, five scenarios spanning general internet fraud: phishing, fake job postings,
  social media, fraudulent services, impersonation. Relevant to LLM-based fraud detection
  broadly; **not an SMS benchmark**, despite often being grouped as one.

## Datasets

- [Balanced dataset for spam and smishing detection using LLMs](https://data.mendeley.com/datasets/vmg875v4xs/1)
  — 10,191 labelled SMS messages, balanced across ham, spam and smishing. **Note the
  construction: it is LLM-generated or LLM-assisted, not a corpus of captured real-world
  traffic.** That matters if you intend to claim real-world performance from it.
- [MOZ-Smishing](https://aclanthology.org/2025.africanlp-1.23/) — message content, Portuguese
  and local languages.

Every public dataset listed here is **message content**. I have found no publicly released
dataset representing A2P *traffic* with the operational signals AIT detection needs — request
flows, delivery-receipt sequences, destination-range distributions, MCC/MNC mix or routing
information. General mobile-network datasets exist (anonymised CDR releases, for instance) but
they are not A2P abuse data and are not labelled for it. See
[gaps](#gaps--what-does-not-exist).

## Tools

### Signalling security

- [SigFW](https://github.com/P1sec/SigFW) — open-source signalling firewall. SS7 and Diameter
  filtering, antispoof, antisniff. The only open-source implementation of what FS.11 describes
  that I could identify.
- [SigPloit](https://github.com/SigPloiter/SigPloit) — telecom signalling exploitation framework
  covering SS7, GTP, Diameter and SIP. Offensive; intended for authorised testing and research.

### Messaging infrastructure

Not fraud tools, but you need a working mental model of the pipeline before the abuse patterns
make sense, and both are practical for building a test bench.

- [Jasmin SMS Gateway](https://github.com/jookies/jasmin) — open-source SMPP/HTTP gateway with
  routing and filtering. [Docs](https://docs.jasminsms.com/).
- [Kannel](https://www.kannel.org/) — the long-standing open-source WAP and SMS gateway.

### Fraud detection

Message-side detection has plenty of open-source work — see the research and dataset sections,
where classifiers and fine-tuned models are the norm.

**Traffic-side is where the hole is.** I could find no open-source implementation aimed at
carrier- or aggregator-side AIT detection. That work lives inside commercial firewalls and
aggregator platforms, and it is not described beyond marketing depth.

## Gaps — what does not exist

Written down because the absences are more informative than the entries above, and because
"someone should build this" is more useful when it is specific.

**Read the scope carefully.** Each item below is about **carrier- and aggregator-side AIT** —
not SMS fraud in general. Message-side work is well supplied; saying otherwise would be wrong.
Every claim is bounded by what I could find in publicly accessible sources, and corrections are
the most welcome kind of contribution.

1. **No open AIT traffic dataset.** Every public dataset is message content. Nothing published
   carries the operational signals AIT shows up in — request flows, delivery-receipt sequences,
   destination-range distributions, MCC/MNC mix, routing. The USENIX '25 work used 9.4M
   proprietary logs never released as a research dataset, so the strongest traffic-side paper
   cannot be reproduced or extended.
2. **No purpose-built AIT traffic generator.** Absent real data, synthesis is the workable
   substitute: realistic A2P traffic with injectable, labelled abuse scenarios. General SMPP
   load generators exist; none of them models abuse with ground truth.
3. **No open traffic-side detector.** The signals operators use — destination-range entropy,
   conversion-rate anomaly, time-profile deviation, delivery-receipt patterns — appear in vendor
   material at marketing depth and in no open implementation. Open-source SMS fraud work sits
   almost entirely on message classification or signalling security instead.
4. **No adopted benchmark for the traffic side.** Message-side has several — MOZ-Smishing,
   FraudSMSWalker, the spam corpora. Carrier-side AIT has none, so no two approaches can be
   compared, which is a large part of why that literature is one paper deep.
5. **The key GSMA guidance is not publicly readable.** FS.07, FS.11 and FS.19 shape what the
   whole interconnect does about this, and all three are members-only.
6. **Coverage outside English is thin.** Public message-side datasets are heavily
   English-centric, though work has begun on Portuguese and other lower-resource languages.
   On the traffic side the question does not yet arise.

I am working on items 2–4 at [opena2p](https://github.com/HelmsArt) — disclosed here rather
than quietly seeded through the list.

## Contributing

Additions welcome, particularly traffic-side research, non-English resources, and anything that
closes one of the gaps above. See [CONTRIBUTING.md](CONTRIBUTING.md).

One rule: **every entry needs a reason.** A bare link with no note explaining what it is and why
it belongs here will be sent back. The value of this list is the annotation, not the count.
