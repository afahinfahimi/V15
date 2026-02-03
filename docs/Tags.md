# Tags V13

**Lovable Trade V13 - "Alert Layer" Edition**
**Updated: February 4, 2026**

---

## Overview

Tags are **non-scoring** visual indicators that provide context beyond SA/MC scores. 
They do not modify scores. 
They surface on the Analyzer table, stock detail cards, and alert panels.
Source from API when available. Yahoo and Google Finance and web search.

---

## Tag Colors

### Red Tags (Negative / Risk)
| Tag | Trigger |
|-----|---------|
| SEC | Active SEC investigation, fraud, restatement (Kill Switch) |
| Court | Lawsuit, hearing, investigation, ruling |
| Guide ↓ | Forward guidance lowered |
| EPS ↓ | Consensus EPS estimates cut > 5% (90d) |
| Downgrade | Net analyst downgrades last 30 days |
| Insider ↓ | Net insider selling 6 months |

### Yellow Tags (Caution / Neutral)
| Tag | Trigger |
|-----|---------|
| Earning | Earnings within 14 days |
| IPO | Listed < 12 months |
| Turtle | 52W ±10% AND 1M ±10% |
| Lotto | SA < 35 AND MCap < $500M |

### Green Tags (Positive / Opportunity)
| Tag | Trigger |
|-----|---------|
| Bounce | SA < 35 AND 1M < -15% |
| Jump | SA < 35 AND 1M > +15% |
| Guide ↑ | Forward guidance raised |
| EPS ↑ | Consensus EPS estimates raised > 5% (90d) |
| Upgrade | Net analyst upgrades last 30 days |
| Insider ↑ | Net insider buying 6 months |

---

## Tag Display Rules
- Tags shown as small text pill buttons in the **Tags column** (last column before checkboxes)
- Clicking any tag opens a toggle panel showing all active tags with one-line explanations
- Tag colors by category: Risk (Red-L), Opportunity (Green-L), Fundamental (Blue-L), Event (Orange-L)
- Max ~3 tags visible in column, "+N" overflow for additional
- Display priority: Kill Switch → Event → Risk → Fundamental → Opportunity

---

**End of Flags V12**

