# Awesome Messaging Fraud [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated index of A2P messaging abuse — artificially inflated traffic, SMS pumping, grey
> routes, sender-ID spoofing, smishing and signalling attacks — covering the research, the
> industry guidance, the datasets and the tooling that actually exist.

Artificially inflated traffic alone is estimated to account for **5–40% of international A2P SMS
traffic** depending on region, and cost businesses around **US$1.6 billion in 2023**. Despite
that, almost everything written about it is either a vendor blog post or a paywalled industry
report, and the open-source tooling is close to nonexistent.

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
different economics and different detection surfaces.

| Class | What happens | Who pays |
|---|---|---|
| **AIT / SMS pumping** | Bots trigger OTP or verification sends to attacker-controlled ranges; the attacker takes a cut of the termination fee | The enterprise sending the OTPs |
| **Grey routes** | A2P traffic disguised as P2P to avoid interconnect fees | The terminating operator |
| **Sender-ID spoofing** | Forged alphanumeric sender identity, usually to enable phishing | The impersonated brand, and the recipient |
| **Smishing** | Phishing delivered by SMS | The recipient |
| **Signalling attacks** | SS7/Diameter abuse for interception, location tracking or message manipulation | The subscriber |

Further reading on the economics and the industry response:

- [SMS vs artificially inflated traffic: fighting fraud and restoring trust](https://www.thefastmode.com/expert-opinion/43869-sms-vs-artificially-inflated-traffic-the-steps-for-fighting-fraud-and-restoring-trust)
- [Artificial inflation of traffic threatens messaging](https://sinch.com/blog/artificial-inflation-traffic-ait-growing-threat-messaging-ecosystem/) — Sinch
- [Artificial inflation of traffic](https://docs.smsportal.com/docs/artificial-inflation-of-traffic) — SMSPortal, written as operational documentation rather than marketing

## Industry guidance and standards

The normative material. Much of it is membership-gated, which is itself part of why the open
literature is thin — noted here so you know what you are missing rather than assuming it does
not exist.

- **GSMA FS.11** — SS7 and SIGTRAN network security. Defines the recommended interconnect
  filtering rules; the reference that commercial signalling firewalls implement. *Membership-gated.*
- **GSMA FS.19** — Diameter interconnect security, the 4G counterpart to FS.11. *Membership-gated.*
- GSMA has formally categorised SMS pumping under Artificial Inflation of Traffic and issued
  carrier guidance.
- [Mobile Ecosystem Forum](https://mobileecosystemforum.com/) — runs a working group on
  messaging fraud and publishes some material publicly.
- **i3Forum** — carrier-side fraud working group, active on AIT.

## Research

Papers are grouped by vantage point, because the split is the single most useful thing to
understand about this literature.

### Traffic-side: AIT, pumping and flow analysis

Small. This is the entire freely available academic literature on the traffic view that I have
been able to find — additions very welcome.

- **[Preventing Artificially Inflated SMS Attacks through Large-Scale Traffic Inspection](https://www.usenix.org/conference/usenixsecurity25/presentation/huh)**
  — Huh et al., Samsung Research and Samsung Electronics, USENIX Security '25.
  [PDF](https://www.usenix.org/system/files/usenixsecurity25-huh.pdf).
  **The reference work.** Characterises AIT techniques over 9.4 million SMS request logs and
  finds attackers rely on short-lived email services and reuse common prefixes to generate
  unverified phone and IMEI numbers at speed.
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
- [Fraud-R1: a multi-round benchmark for LLM robustness against fraud and phishing inducement](https://arxiv.org/abs/2502.12904)
  — bilingual, five real-world scenarios.
- [MOZ-Smishing: a benchmark dataset for detecting mobile money frauds](https://aclanthology.org/2025.africanlp-1.23/)
  — crowd-sourced from Mozambican mobile users. One of very few non-English resources.
- [Fine-tuning multilingual language models for low-resource smishing detection using LoRA](https://link.springer.com/chapter/10.1007/978-981-92-0071-9_8)
  — English, Bengali and Swahili; addresses the low-resource-language gap directly.
- [Detection and prevention of smishing attacks](https://arxiv.org/abs/2501.00260) — survey.
- [Machine learning driven smishing detection framework for mobile security](https://arxiv.org/abs/2412.09641)
- [Can you walk me through it? Explainable SMS phishing detection](https://www.usenix.org/system/files/soups2025-wang.pdf)
  — SOUPS '25. The usability side: whether explanations actually help recipients.

## Datasets

- [Balanced dataset for spam and smishing detection using LLMs](https://data.mendeley.com/datasets/vmg875v4xs/1)
  — 10,191 labelled SMS messages. Message content.
- [MOZ-Smishing](https://aclanthology.org/2025.africanlp-1.23/) — message content, Portuguese
  and local languages.

Every public dataset in this space is **message content**. There is no public traffic dataset:
no flow records, no delivery-receipt sequences, no destination-range distributions. See
[gaps](#gaps--what-does-not-exist).

## Tools

### Signalling security

- [SigFW](https://github.com/P1sec/SigFW) — open-source signalling firewall. SS7 and Diameter
  filtering, antispoof, antisniff. The only open implementation of what FS.11 describes.
- [SigPloit](https://github.com/SigPloiter/SigPloit) — telecom signalling exploitation framework
  covering SS7, GTP, Diameter and SIP. Offensive; intended for authorised testing and research.

### Messaging infrastructure

Not fraud tools, but you need a working mental model of the pipeline before the abuse patterns
make sense, and both are practical for building a test bench.

- [Jasmin SMS Gateway](https://github.com/jookies/jasmin) — open-source SMPP/HTTP gateway with
  routing and filtering. [Docs](https://docs.jasminsms.com/).
- [Kannel](https://www.kannel.org/) — the long-standing open-source WAP and SMS gateway.

### Fraud detection

Nothing open source. Detection lives inside commercial firewalls and aggregator platforms.

## Gaps — what does not exist

Written down because the absences are more informative than the entries above, and because
"someone should build this" is more useful when it is specific.

1. **No open traffic dataset.** Every public dataset is message content. Nothing exists for
   flow records, delivery-receipt sequences, destination-range distributions or MCC/MNC mix —
   the signals AIT actually shows up in. The USENIX '25 work used 9.4M proprietary logs that
   were not released, so even the one strong traffic-side paper cannot be built on.
2. **No traffic generator.** Absent real data, the workable substitute is synthesis: a generator
   that emits realistic A2P traffic with injectable abuse patterns. Nothing public does this.
3. **No open detection implementation.** The signals operators use — destination-range entropy,
   conversion-rate anomaly, time-profile deviation, delivery-receipt patterns — are described in
   vendor material at marketing depth and implemented nowhere in the open.
4. **No benchmark.** With no shared data and no shared scenarios, no two detection approaches
   can be compared. This is the reason the traffic-side literature has one paper in it.
5. **The normative documents are gated.** FS.11 and FS.19 define the filtering rules the whole
   interconnect relies on, and you cannot read them without GSMA membership.
6. **Almost nothing is non-English** on the message side, and nothing at all on the traffic side.

I am working on items 2–4 at [opena2p](https://github.com/HelmsArt) — disclosed here rather
than quietly seeded through the list.

## Contributing

Additions welcome, particularly traffic-side research, non-English resources, and anything that
closes one of the gaps above. See [CONTRIBUTING.md](CONTRIBUTING.md).

One rule: **every entry needs a reason.** A bare link with no note explaining what it is and why
it belongs here will be sent back. The value of this list is the annotation, not the count.
