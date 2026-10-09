# Cross-Layer Fingerprint Impersonation Detector

> 🚧 **Status: in development.** This README describes the design and roadmap.

Detects clients that fake their identity on the network. Instead of trusting a single signal, it extracts fingerprints from **one network flow** at several layers and asks one question:

> **Do the characteristics of this connection agree with each other?**

## The idea

| Layer | Fingerprint | What it reveals |
|---|---|---|
| TCP / OS network stack | **JA4T** | Operating-system behavior (TTL, window size, MSS, options) |
| TLS | **JA4** | The TLS client (version, ciphers, extensions) |
| HTTP | **JA4H** | Client behavior (headers, order, User-Agent) |

**Example:** TLS looks like Chrome on Windows, TCP looks like Linux, and HTTP looks like a Python client. That is a cross-layer inconsistency, so the flow is flagged.

## Design principles

- **IP ≠ identity.** NAT, VPNs and proxies mean many devices can share one IP, so every flow is analyzed on its own.
- **No single parameter decides.** Weighted scores from TLS, TCP, HTTP, certificate and behavior checks feed the final verdict.
- **Thresholds and weights are tested, not guessed.** They are configurable and tuned using labeled data.
- **A single mismatch is not automatically malicious.**

## Planned features

- Live capture and PCAP loading, with flow generation
- JA4 / JA4T / JA4H extraction from the same flow
- Certificate analysis (issuer, SAN, validity, chain, consistency)
- Reference identity database (expected client / OS / browser behavior)
- Cross-layer comparison and mismatch-layer identification
- Consistency / risk scoring with verdicts: `CONSISTENT` · `SUSPICIOUS` · `FLAGGED`
- Plain-language explanations of each verdict
- Audit logging to CSV and dataset generation
- Ground-truth labeling and evaluation (accuracy, precision, recall, F1, confusion matrix)
- Graphs, statistics and a final report

## Roadmap

- [ ] **Phase 1:** capture → flow generation → packet parsing
- [ ] **Phase 2:** JA4, JA4T, JA4H extraction
- [ ] **Phase 3:** certificate analysis, reference table, cross-layer comparison
- [ ] **Phase 4:** packet and behavior parameters, scoring
- [ ] **Phase 5:** spoofing detection, shared-IP handling, thresholds
- [ ] **Phase 6:** audit CSV, dataset generation, ground-truth labels
- [ ] **Phase 7:** evaluation metrics and confusion matrix
- [ ] **Phase 8:** graphs, statistics, final report
- [ ] **Phase 9:** explanation layer, testing, optimization

## Planned test scenarios

Normal Chrome and Firefox, different operating systems, spoofed Chrome (e.g. curl-impersonate), shared public IPs, multiple simultaneous connections, invalid or expired certificates, and unusual but legitimate traffic.

## Ethics

Traffic analysis is performed only on captures and networks I own or am authorized to test, in an isolated lab.
