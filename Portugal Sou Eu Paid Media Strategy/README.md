# 🎯 Media Strategy & Analytics — National Production Accreditation Program
 
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


![Google Ads Campaigns Overview](https://i.imgur.com/4uzUuF1.png)

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

## 📊 Analytics, Tracking & Measurement
 
### Google Tag Manager — Container Architecture
 
A GTM container was implemented as the central tag management layer, coordinating all tracking across GA4, Google Ads, Meta Pixel, and LinkedIn Insight. The container manages 36+ tags organized across three main platforms:
 
**Tags fired on page load:**
- GA4 SETUP (Google Tag — base configuration)
- Google Analytics Universal Analytics (legacy, pre-existing)
- Google Ads Conversion Tracking (PageView event)
- Meta Pageview Tracking (Custom HTML)
- LinkedIn Insight Tag
 
**Tags fired on user interaction (custom events):**
- GA4 Event tags: `contact_us`, `file_download`, `generate_lead`, `login`, `social_link_click`, `start_form`
- Google Ads Conversion tags mirroring each GA4 event for cross-platform attribution
- Consent-based firing rules aligned with CMP (Consent Management Platform)
 
The container includes a full consent initialization sequence (Consent Initialization → Container Loaded → History Change → Set → Consent Default → Consent Update), ensuring GDPR-compliant tag firing based on user opt-in categories: functional, performance, statistics, and marketing.
 
![GTM Tag Assistant — Tags Fired Summary](https://i.imgur.com/1sPBceA.png)

*Tag Assistant preview showing 5 tags fired on page load across GA4, Google Ads, Meta, and LinkedIn, with consent-aware firing sequence on the left panel.*
 
![GTM Workspace — Full Tag Inventory](https://i.imgur.com/eFElBq0.png)

*GTM workspace showing the complete tag inventory: GA4 events, Google Ads conversion tracking mirrors, Meta Pixel, and legacy tags.*
 
### dataLayer Specification
 
A structured `dataLayer` was designed and documented in a measurement plan, serving as the bridge between the website's front-end and GTM. Each user action pushes a specific event with associated parameters:
 
| dataLayer Event | Conversion? | Trigger | Parameters |
|---|---|---|---|
| `page_view` | No | Page Load | `user_id` |
| `generate_lead` | **Yes** | Form submission (full registration) | `type`, `services` |
| `start_form` | No | User begins filling the form | `type` |
| `contact_us` | No | Contact form submission | — |
| `login` | No | Successful login | `method` |
| `file_download` | **Yes** | Simulator download click | — |
| `social_link_click` | No | Social media link click | `method` |
 
**Variable definitions** used across the dataLayer:
 
| Variable | Type | Source | Description | Example Values |
|---|---|---|---|---|
| `method` | string | HTML | Login method or social network | `"E-mail"`, `"Google"`, `"Facebook"` |
| `type` | string | HTML | Type of lead action | `"registration"` |
| `services` | string | HTML | Areas of interest selected in form | User-selected services |
 
The dataLayer push is declared **before** the GTM library loads (`gtm.js` event), ensuring all variables are available when tags fire. QA was performed on each event with pass/fail status tracked in the measurement plan.
 
### GA4 Property Configuration
 
The GA4 property was configured with the following specifications:
 
- **Data Stream:** Web (single stream)
- **Timezone:** Portugal (GMT+00:00)
- **Currency:** EUR (€)
- **Enhanced Measurement:** Enabled — scrolls, outbound clicks, site search, video engagement, file downloads, and history-based page changes all active. Form interactions disabled (handled via custom dataLayer events for finer control).
- **Cross-domain tracking:** Configured via Google Tag settings
 
### Conversion Tracking Pipeline
 
A three-layer conversion tracking architecture ensures data flows correctly from user action to reporting:
 
```
User Action → dataLayer.push() → GTM Trigger → GA4 Event Tag
                                              → Google Ads Conversion Tag
                                              → Meta Pixel Event (where applicable)
```
 
Each conversion event has a dedicated Google Ads conversion action with unique Conversion ID and Label pairs, enabling accurate attribution in Google Ads reporting. The primary conversion (`generate_lead`) tracks completed B2B registrations, while secondary conversions (`file_download`, `start_form`) provide supporting signals for Smart Bidding optimization.
 
### UTM & Campaign Naming Convention
 
A standardized UTM structure was implemented for clean attribution in GA4:
 
```
?utm_source={platform}&utm_medium=cpc&utm_campaign={campaign_name}&utm_id={campaign_id}
```
 
- **Google Ads:** `utm_source=gads`
- **Meta Ads:** `utm_source=meta`
- **Campaign naming:** `ClientName_CampaignType_KeywordType_CampaignName_GoalType`
 

### Looker Studio — Custom Reporting Dashboard

A comprehensive, branded Looker Studio dashboard was built to centralize performance reporting across two main sections:

#### 🟢 Website & Traffic Analytics (GA4 Source)

Multi-page dashboard covering:
- **Traffic Overview** — Sessions, page views, engagement rate, bounce rate with period-over-period comparison
- **Channel Performance** — Breakdown by Direct, Organic Search, Paid Search, Referral, Social with conversion attribution
- **Page-Level Analysis** — Landing page and page visit performance tables
- **User Characteristics** — Total, active, daily, and monthly active user metrics



![Traffic Overview](https://i.imgur.com/uj7qutw.png)

*March 2026: 1,215 sessions (+17.5%), 69.05% interaction rate (-10.4%), 642 total users (+23.2%).*

![Channel Performance](https://i.imgur.com/68MPZXs.jpeg)

*Performance by channel with sessions, users, interaction rate, conversions, and time-on-site metrics. Direct (606 sessions) and Organic Search (470 sessions) lead traffic.*

![Page & User Analysis](https://i.imgur.com/hm1E3mH.png)

*Top landing pages and visited pages with session counts. 638 active users, 16,199 monthly active users.*



#### 🟡 Google Ads Performance (Ads Data Source)

Dedicated Google Ads reporting section featuring:
- **High-Level KPIs** — Impressions, Clicks, CTR, Investment, Conversions, Conv. Rate, Cost per Conversion
- **Campaign & Ad Group Tables** — Detailed breakdown with investment, impressions, clicks, CTR, and conversion metrics
- **Chronological Evolution** — Annual and monthly investment vs. conversions and investment vs. clicks charts
- **Device Segmentation** — Investment and conversion performance by device type



![Google Ads Overview](https://i.imgur.com/5v8s5sw.png)

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
| **Tag Management** | Google Tag Manager (36+ tags, consent-aware firing) |
| **Analytics** | GA4 (custom events + Enhanced Measurement), dataLayer architecture |
| **Conversion Tracking** | GA4 Events, Google Ads Conversion Tags, Meta Pixel |
| **Reporting** | Looker Studio (custom multi-page dashboards) |
| **Campaign Management** | Google Ads Editor, structured optimization log |
| **Attribution** | UTM framework, GA4 channel groupings, cross-platform conversion pipeline |
| **Compliance** | CMP integration, consent-based tag firing (GDPR) |

---

## 💡 Key Takeaways

1. **Starting from zero requires patience** — With no historical data, Smart Bidding strategies need time to exit learning phases. The tCPA adjustment from €20 to €40 was critical to avoid under-delivery.

2. **Dashboard-first approach pays off** — Building comprehensive Looker Studio dashboards from day one created a shared source of truth with the client and accelerated decision-making.

3. **B2B lead gen ≠ B2C e-commerce** — A 15-field registration form demanded a different optimization mindset. Recommendations for form simplification and two-step flows were part of the ongoing UX conversation.

4. **Broad match + AI bidding is a viable cold-start strategy** — In a niche B2B market with limited search volume, broad match keywords with Maximize Conversions bidding allowed the algorithm to explore and learn without excessive manual keyword management.

5. **A measurement plan is the foundation** — Documenting every dataLayer event, variable, conversion action, and QA status in a structured spreadsheet before implementation prevented tracking gaps and enabled systematic cross-platform attribution from day one.

---

## 📎 Project Artefacts

- Media Strategy Brief & Channel Allocation
- Measurement Plan (dataLayer specs, GA4 configuration, conversion tracking codes, UTM structure)
- Campaign Template (keyword matrix, ad copy, UTM structure)
- Google Ads account setup & campaign architecture
- GTM container setup (36+ tags, consent-aware architecture)
- GA4 tracking implementation with custom dataLayer events
- Google Ads & Meta Pixel conversion tracking pipeline
- Looker Studio dashboard (GA4 + Google Ads data sources)
- Optimization log with documented changes and rationale

---

<sub>📌 *Client name and project identity anonymized for confidentiality. All data shown is from actual campaign performance. Screenshots may contain redacted elements.*</sub>
