# RiskLens — Network Risk Intelligence Platform

**Live dashboard:** https://gunasheela112-lab.github.io/RiskLens-/
**Analysis notebook (Colab):** https://colab.research.google.com/drive/1Y1y1dE_xD8kuW1TSc5FeXo8YkdZqtfuW?usp=sharing

---

## The Problem

Most network security tools tell you *what's* wrong — an open port here, a misconfigured role there. They rarely tell you *how much it matters*. A security team looking at a wall of alerts still has to guess which one to fix first.

RiskLens was built to answer that question directly: given a network's topology, its misconfigurations, its traffic history, and its operating cost, **which resource should you fix first, and how much safer does the network get once you do?**

## The Scenario

RiskLens analyzes a simulated 35-resource AWS environment spread across three VPCs — `Production`, `Staging`, and `Shared-Services` — complete with realistic network connectivity, IAM permission levels, encryption/MFA status, 120 days of traffic history, and monthly cloud cost data per resource.

## How It Works

**1. Network graph & blast radius.** Every resource is a node in a graph, connected by its actual network paths (subnet routing, peering, shared-services links). For each resource, RiskLens calculates a *blast radius score* — how much of the network is reachable if that one resource is compromised. A vulnerable but isolated resource is far less dangerous than a vulnerable, well-connected one, and the scoring reflects that directly.

**2. Vulnerability scoring.** Each resource is scored on its actual misconfigurations: IAM permission level, public exposure, encryption status, MFA enforcement, and open ports.

**3. Trend forecasting.** Using 120 days of traffic and alert history, a regression model classifies each resource as *rising*, *flat*, or *improving* in risk — so the score reflects where a resource is heading, not just where it stands today.

**4. Composite Risk Score & Network Badge.** Vulnerability and blast radius combine multiplicatively (a weak, well-connected resource scores far higher than an equally weak, isolated one), adjusted by trend. All resource scores roll up into a single **Network Risk Score out of 1000** — currently sitting at **863/1000** for this simulated environment.

**5. Cost correlation.** Resources are cross-checked against their monthly cost, flagging cases that are both high-risk *and* expensive — the clearest candidates where remediation is both a security and a financial win.

**6. Ranked fix-list.** Instead of a flat alert feed, RiskLens outputs a prioritized list of what to fix first — the same kind of output a real risk analyst would hand to a manager.

## Worked Example: Fixing `res-002`

`res-002`, a Shared-Services load balancer, surfaced as the network's single highest risk: publicly exposed, admin-level IAM role, unencrypted, no MFA, and rising traffic anomalies — plus it was among the most expensive resources in the network. It sat at a risk score of **100/100**.

Simulating a fix (least-privilege IAM, encryption enabled, MFA enforced, exposure closed) drops its individual score to **2.1/100** and raises the overall **Network Risk Score from 863 to 891** — a 28-point improvement from resolving a single resource. This is the core proof point of RiskLens: the scoring model isn't just descriptive, it's actionable, and the impact of a fix is directly measurable.

## The Interactive Dashboard

The [live dashboard](https://gunasheela112-lab.github.io/RiskLens-/) lets you explore this directly:
- Click any node in the network graph to see its full risk profile
- Hit **Simulate Fix** on any resource and watch the Network Risk Score badge update live
- Review the ranked fix-list, the cost-vs-risk scatter plot, and the 120-day trend lines for the fastest-rising resources

## What's in This Repo

| File | Purpose |
|---|---|
| `index.html` | Interactive dashboard (also live via GitHub Pages) |
| Colab notebook (linked above) | Full data generation, scoring model, and analysis pipeline |
| `nodes.csv`, `edges.csv` | Network topology |
| `misconfigurations.csv` | Per-resource security configuration |
| `risk_events.csv` | 120-day traffic/anomaly history |
| `cost_data.csv` | Monthly cost per resource |
| `risklens_scores.csv` | Final composite risk scores for all resources |
| `risklens_fixlist_top10.csv` | Ranked priority fix list |
| `before_after_summary.csv` | The `res-002` remediation worked example |
| `chart1`–`chart5` PNGs | EDA and result visualizations |
| `RiskLens_Report.pdf` | One-page exportable risk report |

## Tech Stack

Python (pandas, NumPy, NetworkX, scikit-learn) for data generation, graph analysis, and trend forecasting · Matplotlib for visualization · ReportLab for PDF generation · vanilla HTML/CSS/JavaScript (SVG-based) for the interactive dashboard — no frameworks, fully self-contained, works offline once loaded.

---

*Built as a portfolio project demonstrating combined networking, cloud security, and data analysis skills. The underlying network is simulated for demonstration purposes.*
