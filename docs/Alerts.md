# Alerts V13

**Lovable Trade V13 - "Alert Layer" Edition**
**Updated: February 4, 2026**

---

## Overview

Alerts are **non-scoring** visual indicators that provide context beyond SA/MC scores. 
They do not modify scores. 
They surface on the Analyzer table, stock detail cards, and alert panels.
Source from API when available. Yahoo and Google Finance and web search.

---

## Alerts Colors

### Red Alerts (Negative / Risk)
| Alert | Trigger |
|-----|---------|
| SEC | Active SEC investigation, fraud, restatement (Kill Switch) |
| Court | Lawsuit, hearing, investigation, ruling |
| Guide ↓ | Forward guidance lowered |
| EPS ↓ | Consensus EPS estimates cut > 5% (90d) |
| Downgrade | Net analyst downgrades last 30 days |
| Insider ↓ | Net insider selling 6 months |

### Yellow Alerts (Caution / Neutral)
| Alert | Trigger |
|-----|---------|
| Earning | Earnings within 14 days |
| IPO | Listed < 12 months |
| Turtle | 52W ±10% AND 1M ±10% |
| Lotto | SA < 35 AND MCap < $500M |

### Green Alerts (Positive / Opportunity)
| Alert | Trigger |
|-----|---------|
| Bounce | SA < 35 AND 1M < -15% |
| Jump | SA < 35 AND 1M > +15% |
| Guide ↑ | Forward guidance raised |
| EPS ↑ | Consensus EPS estimates raised > 5% (90d) |
| Upgrade | Net analyst upgrades last 30 days |
| Insider ↑ | Net insider buying 6 months |

---

## Alert Display Rules
- Alerts shown as small text pill buttons in the **Alerts column** (last column before checkboxes)
- Clicking any Alert opens a toggle panel showing all active Alerts with one-line explanations
- Alert colors by category: Risk (Red-L), Opportunity (Green-L), Fundamental (Blue-L), Event (Orange-L)
- Max ~3 Alerts visible in column, "+N" overflow for additional
- Display priority: Kill Switch → Event → Risk → Fundamental → Opportunity

---

**End of Alerts Instructions**

