# 🎯 Media Strategy & Analytics — National Certification Program
 
> **Industry:** B2B · Institutional / Trade Association  
> **Market:** Portugal  
> **Duration:** Q1 2026 (ongoing)  
> **Channels:** Google Ads · Meta Ads  
> **Analytics:** Google Analytics 4 · Looker Studio · Google Tag Manager

---

## 📋 Overview

End-to-end media strategy and analytics implementation for a national certification program aimed at promoting domestically produced goods and services. The project encompassed paid media planning, campaign setup & optimization, GA4 tracking architecture, and custom Looker Studio dashboards for performance reporting.

The client — a national trade association — sought to drive **B2B lead generation** (company registrations) through a digital-first approach, transitioning from a primarily institutional communication model to a performance-driven media framework.

---

## 🧩 Challenge

- **Low digital maturity:** The client had no prior paid media activity and limited tracking infrastructure.
- **Complex registration flow:** A 15-field B2B form on the website created a high-friction conversion path.
- **Niche B2B audience:** Targeting Portuguese businesses across multiple industries interested in national certification.
- **No historical data:** Campaigns launched from scratch — no baseline for benchmarking or audience modelling.

---

## 🗺️ Strategy

### Full-Funnel Media Plan

A structured funnel approach was designed to move prospects from awareness through to conversion and retention:

| Funnel Stage | Channel | Objective | Formats | Primary KPIs |
|---|---|---|---|---|
| **Awareness** | Meta Ads, YouTube | Brand visibility & engagement | Video, Carousel, Stories | Impressions, Video Views |
| **Consideration** | Google Search | Drive qualified traffic | Responsive Search Ads | CTR, CPC |
| **Conversion** | Google PMax + Search | Maximize lead registrations | Smart Creatives, RSAs | Cost per Lead, Conversions |
| **Retargeting** | Meta Ads, Display | Re-engage non-converters | Static, Dynamic, Video | ROAS, Conversions |

### Budget Allocation (Phase 1)

- **Google Ads** (Performance Max + Search): ~74% of media budget
- **Meta Ads** (Awareness + Retargeting): ~26% of media budget
- **LinkedIn Ads** planned for Phase 2 targeting niche B2B segments

---

## ⚙️ Implementation

### Google Ads — Campaign Architecture

The Google Ads account was structured around three active Search campaigns using broad match with AI-powered bidding (Maximize Conversions), plus a Performance Max campaign:

| Campaign Type | Focus | Bidding Strategy |
|---|---|---|
| Search — Brand | Brand terms & program awareness | Maximize Conversions |
| Search — Products | National product categories (food, artisan, wine) | Maximize Conversions |
| Search — Business Registration | Direct registration intent | Maximize Conversions |
| Performance Max | Full-funnel automated lead gen | Maximize Conversions |

**Ad Groups** were structured thematically:
- Brand awareness messaging
- Program benefits & certification value
- Product-specific verticals (Gourmet, Gastronomy, Wine & Olive Oil, Artisan)
- National brand ("Made in Portugal") messaging
- Trust & certification stamp messaging



![Google Ads Campaigns Overview](https://i.imgur.com/68MPZXs.jpeg)
*Campaign-level view showing budget allocation, interaction rates, and optimization scores across active campaigns.*

![Google Ads Ad Groups](https://i.imgur.com/aQPMwr0.png)
*Ad group breakdown with interaction rates ranging from 1.53% to 26.47% across thematic groups.*



### Ongoing Optimizations

Campaign management included iterative optimization documented in a structured log:

- **tCPA adjustments** — Increased target CPA from €20 to €40 to control delivery pacing against low initial conversion volume
- **Ad Strength improvements** — Iterative headline and description refinements to push ad strength from "Poor" toward "Good/Excellent"
- **Keyword expansion** — Broad match keywords across brand, product, and registration intent themes
- **UTM architecture** — Consistent UTM tagging across all campaigns for clean attribution in GA4

---

## 📊 Analytics & Dashboards

### GA4 Implementation

- Full GA4 tracking setup via Google Tag Manager
- Conversion tracking for form submissions (lead generation)
- Event tracking for micro-conversions (scroll depth, partial form fills, page engagement)
- Channel grouping aligned with paid media taxonomy

### Looker Studio — Custom Reporting Dashboard

A comprehensive, branded Looker Studio dashboard was built to centralize performance reporting across two main sections:

#### 🟢 Website & Traffic Analytics (GA4 Source)

Multi-page dashboard covering:
- **Traffic Overview** — Sessions, page views, engagement rate, bounce rate with period-over-period comparison
- **Channel Performance** — Breakdown by Direct, Organic Search, Paid Search, Referral, Social with conversion attribution
- **Page-Level Analysis** — Landing page and page visit performance tables
- **User Characteristics** — Total, active, daily, and monthly active user metrics



![Traffic Overview](https://i.imgur.com/68MPZXs.jpeg)
*March 2026: 1,215 sessions (+17.5%), 69.05% interaction rate (-10.4%), 642 total users (+23.2%).*

![Channel Performance](https://i.imgur.com/68MPZXs.jpeg)
*Performance by channel with sessions, users, interaction rate, conversions, and time-on-site metrics. Direct (606 sessions) and Organic Search (470 sessions) lead traffic.*

![Page & User Analysis](https://i.imgur.com/TiQ2Nk2.png)
*Top landing pages and visited pages with session counts. 638 active users, 16,199 monthly active users.*



#### 🟡 Google Ads Performance (Ads Data Source)

Dedicated Google Ads reporting section featuring:
- **High-Level KPIs** — Impressions, Clicks, CTR, Investment, Conversions, Conv. Rate, Cost per Conversion
- **Campaign & Ad Group Tables** — Detailed breakdown with investment, impressions, clicks, CTR, and conversion metrics
- **Chronological Evolution** — Annual and monthly investment vs. conversions and investment vs. clicks charts
- **Device Segmentation** — Investment and conversion performance by device type



![Google Ads Overview](https://i.imgur.com/4uzUuF1.png)
*March 2026: 1,186 impressions, 63 clicks, 5.31% CTR, €51.08 investment, 1 conversion at €51.08 CPA.*

![Campaign Detail Tables](https://i.imgur.com/RnwrwGO.png)
*Campaign and ad group level reporting with investment, impressions, clicks, CTR, CPC, and conversion data.*

![Investment Evolution](https://i.imgur.com/wVeJ2Qi.png)
*Monthly and annual investment vs. clicks/conversions trend charts showing ramp-up phase.*



---

## 📈 Key Results (March 2026 — Early Phase)

> ⚠️ *Results reflect the initial ramp-up period (first weeks of campaign activation). Performance is expected to improve as campaigns exit learning phase and conversion data accumulates.*

| Metric | Value |
|---|---|
| **Total Sessions (Website)** | 1,215 (+17.5% MoM) |
| **Total Users** | 642 (+23.2% MoM) |
| **Interaction Rate** | 69.05% |
| **Google Ads Impressions** | 1,186 |
| **Google Ads Clicks** | 63 |
| **Google Ads CTR** | 5.31% |
| **Google Ads Investment** | €51.08 |
| **Avg. CPC** | €0.81 |
| **Conversions (Leads)** | 1 |
| **Top Channel — Sessions** | Direct (606) |
| **Top Channel — Conversions** | Direct (78) |
| **Organic Search Growth** | +27.0% sessions MoM |

---

## 🛠️ Tech Stack

| Layer | Tools |
|---|---|
| **Paid Media** | Google Ads (Search + PMax), Meta Ads |
| **Tracking** | Google Tag Manager, GA4 |
| **Reporting** | Looker Studio (custom dashboards) |
| **Campaign Management** | Google Ads Editor, structured optimization log |
| **Attribution** | UTM framework, GA4 channel groupings |

---

## 💡 Key Takeaways

1. **Starting from zero requires patience** — With no historical data, Smart Bidding strategies need time to exit learning phases. The tCPA adjustment from €20 to €40 was critical to avoid under-delivery.

2. **Dashboard-first approach pays off** — Building comprehensive Looker Studio dashboards from day one created a shared source of truth with the client and accelerated decision-making.

3. **B2B lead gen ≠ B2C e-commerce** — A 15-field registration form demanded a different optimization mindset. Recommendations for form simplification and two-step flows were part of the ongoing UX conversation.

4. **Broad match + AI bidding is a viable cold-start strategy** — In a niche B2B market with limited search volume, broad match keywords with Maximize Conversions bidding allowed the algorithm to explore and learn without excessive manual keyword management.

---

## 📎 Project Artefacts

- Media Strategy Brief & Channel Allocation
- Campaign Template (keyword matrix, ad copy, UTM structure)
- Google Ads account setup & campaign architecture
- GA4 tracking implementation (GTM)
- Looker Studio dashboard (GA4 + Google Ads data sources)
- Optimization log with documented changes and rationale

---

<sub>📌 *Client name and project identity anonymized for confidentiality. All data shown is from actual campaign performance. Screenshots may contain redacted elements.*</sub>
