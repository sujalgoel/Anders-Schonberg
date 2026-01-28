# Project Proposal - Review Scraping & LLM Extraction System

**Client:** Anders Schonberg

**Prepared by:** Sujal

**Date:** January 28, 2026

**Estimated Timeline:** 3-6 weeks (phased approach)

---

## Executive Summary

After analyzing the project requirements in detail, I'm proposing a **phased delivery approach** that balances speed-to-value with production-grade quality. This allows you to start extracting competitive insights within 3 weeks while building toward a fully automated, scalable system.

**Key Recommendation:** Start with an MVP focusing on your top 5 competitors across G2 and Product Hunt, then scale to all 25 products with full automation in Phase 2.

---

## Understanding of Requirements

### Core Objectives

- Extract verbatim customer voice from competitor reviews (G2, Capterra family, Product Hunt)
- Answer 8 market questions through structured fact extraction
- Enable querying by product, pain point, feature, adoption pattern
- Run weekly, automated with no manual intervention
- Store raw data immutably, extract facts separately

### Critical Requirements

1. **Quote anchoring:** Extracted quotes must be literal substrings of original text (95% validation)
2. **Idempotency:** Re-running scrapers doesn't duplicate data
3. **Verbatim capture:** No cleaning, summarization, or interpretation at ingestion
4. **Controlled vocabulary:** Consistent labels for facts (e.g., "Onboarding" not "Initial Setup")
5. **Weekly automation:** Runs unattended, logs errors, alerts on failures

### Data Sources

- **G2** (primary, requires free account for pagination)
- **Capterra/GetApp/Software Advice** (treated as one family)
- **Product Hunt** (weekly, innovation signals only)

### Fact Types (8)

1. `reason_chosen` - Why customers selected the product
2. `unmet_need` - Pain points and complaints
3. `loved_feature` - Features with positive sentiment
4. `feature_not_used` - Ignored or failed adoption
5. `alternative_considered` - Products they compared or replaced
6. `new_and_loved` - Innovation signals (Product Hunt focus)
7. `adoption_spread` - How product spreads within teams
8. `adoption_blocker` - Barriers to team-wide adoption

---

## Technical Architecture

### Stack

- **Scraping:** Python + Playwright (or Apify Actors)
- **LLM Extraction:** OpenAI GPT-4o or Claude 3.5 Sonnet
- **Storage:** Azure Blob Storage (raw JSONL) + Azure SQL Database (facts)
- **Orchestration:** Azure Functions (or Apify Scheduled Actors)
- **Monitoring:** Azure Application Insights + custom logging

### Data Flow

```
[G2/Capterra/PH]
    → Scraper (Playwright)
    → Raw JSONL (Azure Blob /raw/reviews/)
    → LLM Extraction (batch 10-25 reviews)
    → Facts JSONL (Azure Blob /facts/)
    → SQL Database (queryable)
    → Weekly Summary Reports
```

### File Structure

```
/raw/
  /reviews/
    g2_jira_2026-01-28.jsonl
    capterra_clickup_2026-01-28.jsonl
  /product_hunt/
    ph_weekly_2026-01-28.jsonl
/facts/
  facts_2026-01-28_v1.jsonl
/runs/
  run_log_2026-01-28.json
```

---

## Phased Delivery Plan

### Phase 1: MVP - Proof of Concept (55-60 hours, 3 weeks)

**Scope:**

- G2 scraper (5 products: Jira, ClickUp, Asana, Monday.com, Notion)
- Product Hunt scraper (weekly top 20 in productivity category)
- LLM extraction pipeline (all 8 fact types)
- Azure Blob + SQL storage
- Manual trigger (not fully automated yet)
- 85-90% quote validation target

**Deliverables:**

- ✅ Working scrapers for G2 + Product Hunt
- ✅ ~2,500 reviews ingested (5 products × ~500 reviews)
- ✅ Extracted facts in SQL database
- ✅ Sample queries demonstrating insights
- ✅ Documentation for running manually
- ✅ Controlled vocabulary (initial version, co-created with you)

**Time Breakdown:**

| Task                                                 | Hours     |
| ---------------------------------------------------- | --------- |
| G2 scraper (auth, pagination, anti-bot, idempotency) | 18-20     |
| Product Hunt scraper/API                             | 3-5       |
| LLM extraction pipeline + prompts                    | 12-14     |
| Quote anchoring validation (v1)                      | 6-8       |
| Controlled vocabulary workshop + implementation      | 5-6       |
| Azure Blob + SQL setup                               | 6-8       |
| Testing with 5 products                              | 3-5       |
| **Total**                                            | **55-60** |

**Why This Works:**

- Proves the concept with your most important competitors
- You can start extracting insights immediately
- We iterate on extraction quality together with real data
- Lower upfront investment, faster ROI
- Validates technical approach before scaling

---

### Phase 2: Scale & Automate (45-50 hours, 2-3 weeks)

**Scope:**

- Add Capterra family scrapers (Capterra, GetApp, Software Advice)
- Expand to all 25 competitor products
- Full weekly automation (Azure Functions scheduled)
- Monitoring, alerting, error recovery
- 95% quote validation target
- Refined controlled vocabulary based on Phase 1 learnings

**Deliverables:**

- ✅ All 3 review platforms operational
- ✅ ~12,500 reviews ingested (25 products)
- ✅ Weekly automated runs (zero manual intervention)
- ✅ Error monitoring + alerts (DOM breakage, volume drops)
- ✅ Run logs and health dashboard
- ✅ Final controlled vocabulary
- ✅ Comprehensive documentation

**Time Breakdown:**

| Task                                    | Hours     |
| --------------------------------------- | --------- |
| Capterra family scrapers (3 sites)      | 16-18     |
| Expand to 25 products (testing, tuning) | 6-8       |
| Weekly automation (Azure Functions)     | 6-8       |
| Monitoring & alerting system            | 5-6       |
| Quote validation refinement (→95%)      | 3-5       |
| Controlled vocabulary refinement        | 2-3       |
| End-to-end testing & bug fixes          | 3-4       |
| **Total**                               | **45-50** |

---

### Phase 3: Ongoing Maintenance (Monthly Retainer)

**Scope:**

- Fix DOM breakage within 3 business days (sites change frequently)
- LLM prompt refinement as quality issues emerge
- Add new competitors as requested
- Monthly data quality reports
- Infrastructure monitoring

**Estimated Effort:** 5-10 hours/month (variable based on site changes)

---

## Detailed Technical Implementation

### 1. Scraping Strategy

**G2 Scraper:**

```python
# Key features:
- Login with free account (stored securely in Azure Key Vault)
- Pagination handling (iterate through review pages)
- Expandable content ("Read more" button clicks)
- Extract: review text, rating, reviewer role, date, title
- Generate stable review_id: sha1(product + reviewer + date + text_hash)
- Rate limiting: 2-3 seconds between requests, randomized
- User-agent rotation
- Store raw HTML for debugging
```

**Capterra Family:**

```python
# Shared scraper with site-specific selectors
- No auth required (public reviews)
- Handle infinite scroll
- Dedupe across Capterra/GetApp/Software Advice (same parent company)
- Treat as single source (set source = "Capterra Family")
```

**Product Hunt:**

```python
# API preferred (if available), fallback to scraping
- Weekly top 20-30 products in "Productivity" category
- Extract: product name, tagline, upvotes, top 10 comments
- Flag maker comments if identifiable
- Focus on new products (launched within last 3 months)
```

**Idempotency Logic:**

```python
# Prevent duplicates across runs
1. Generate deterministic review_id on scrape
2. Before writing, check if review_id exists in existing JSONL
3. Only append new reviews
4. Track last_scraped_at per product in metadata file
5. Optional: use since_date parameter for incremental backfill
```

### 2. LLM Extraction Pipeline

**Prompt Structure:**

```
System: You are a market research fact extractor...

User: Extract facts from these reviews following strict rules:
- Only explicitly stated facts (no inference)
- Quote must be verbatim substring from original text
- Use controlled vocabulary for labels
- Return JSON with fact_type, label, entity, quote, confidence

[Batch of 10-25 reviews in JSON format]

Example output:
{
  "fact_type": "reason_chosen",
  "label": "Ease of Use",
  "entity": "Jira",
  "quote": "I chose Jira because the development team was already familiar with it",
  "confidence": 0.95
}
```

**Batch Processing:**

```python
1. Group reviews into batches of 20 (optimal token usage)
2. Send to LLM (GPT-4o or Claude 3.5 Sonnet)
3. Parse JSON response
4. Validate schema + enums
5. Run quote anchoring validation
6. Retry failed batches with smaller size (10 reviews)
7. Log invalid facts separately (not silently dropped)
```

**Quote Anchoring Validation:**

```python
def validate_quote(original_text, extracted_quote):
    # Normalize both strings
    original_norm = normalize(original_text)  # handle unicode, spaces, punctuation
    quote_norm = normalize(extracted_quote)

    # Check if quote is substring
    if quote_norm in original_norm:
        return True

    # Fuzzy match for minor variations (90% similarity threshold)
    similarity = levenshtein_ratio(quote_norm, closest_substring(original_norm))
    return similarity >= 0.90
```

**Controlled Vocabulary (Initial Draft):**

```
# Will refine with you in Phase 1
Ease of Use, Onboarding, Automation, Collaboration,
Reporting, Customization, Integrations, Performance,
Mobile Experience, Notifications, Pricing, Support,
Learning Curve, Complexity, Flexibility, UI/UX,
Team Adoption, Permissions, Search, Templates
```

---

## Risks & Mitigations

| Risk                                 | Impact | Mitigation                                                            |
| ------------------------------------ | ------ | --------------------------------------------------------------------- |
| **Sites block/rate-limit scrapers**  | High   | Human-like delays, user-agent rotation, residential proxies if needed |
| **DOM structure changes**            | High   | Modular selectors, automated breakage detection, 3-day fix SLA        |
| **LLM extraction quality <95%**      | Medium | Iterative prompt refinement, controlled vocabulary, human spot-checks |
| **Quote anchoring edge cases**       | Medium | Robust normalization, fuzzy matching, log failures for analysis       |
| **Scale: 25 products × 500 reviews** | Medium | Batch processing, parallel scraping, efficient rate limiting          |
| **Azure storage costs**              | Low    | JSONL is compressed, ~100MB per weekly run                            |

---

## Critical Questions to Clarify

Before starting, I need answers to:

1. **Controlled Vocabulary:** Do you have a predefined list of labels, or should we co-create one in Phase 1 based on initial data?
2. **Review Volume:** Roughly how many reviews per product? (all?)
3. **Language Handling:** You said language doesn't matter - should I pass non-English reviews to the LLM as-is, or translate to English first?
4. **Competitor List:** Can you provide the list of 25 products now? (I'll use it for scope validation)
5. **LLM API Key:** Can you provide your OpenAI or Anthropic API key? (Estimated usage: ~$6-7 per weekly run)
6. **Azure Access:** When can I get credentials for Blob Storage + SQL Database? (Needed for Phase 1 kickoff)
7. **Definition of "Weekly":** Should runs happen Monday mornings, or flexible timing?
8. **Product Hunt Focus:** "Productivity" category only, or broader (e.g., AI tools, collaboration, automation)?
9. **Maintenance Phase:** After Phase 2, do you want a monthly retainer for ongoing fixes, or handoff full ownership?

---

## Proposed Timeline

### Phase 1 (3 weeks)

- **Week 1:** G2 scraper + Azure setup + controlled vocabulary workshop
- **Week 2:** Product Hunt scraper + LLM extraction pipeline + quote validation
- **Week 3:** Testing with 5 products + SQL queries + demo

**Delivery:** Working system with 5 competitors, ready for you to query

### Phase 2 (2-3 weeks)

- **Week 4-5:** Capterra scrapers + expand to 25 products + automation
- **Week 6:** Monitoring + refinement + end-to-end testing

**Delivery:** Fully automated weekly system

### Phase 3 (ongoing)

- **Starts Week 7+:** Monthly retainer for maintenance

---

## Alternative: Single-Phase Approach

If you prefer full delivery in one phase (not recommended, but possible):

**Timeline:** 6-7 weeks
**Effort:** 100-110 hours
**Risk:** Higher upfront cost, less flexibility to pivot based on learnings

**I recommend the phased approach** because:

1. You see value faster (3 weeks vs 7 weeks)
2. We can refine extraction quality together with real data
3. Lower upfront risk for both of us
4. Flexibility to pause after Phase 1 if priorities change

---

## Next Steps

1. **Review this plan** and let me know which approach (phased vs. single) you prefer
2. **Answer the 9 critical questions** above (especially controlled vocabulary, competitor list, Azure access, LLM API key)
3. **Contract & Azure credentials** - I can start within 36 hours of receiving these
4. **Weekly check-ins** during development (15 min) to show progress and get feedback

**Note:** My working days are Monday-Friday. Weekends (Saturday and Sunday) are not included in timeline estimates.

---

## Confidence Statement

I'm confident I can deliver a production-grade market intelligence system that answers your 8 core questions with verbatim quotes from competitor reviews.

The phased approach balances speed (3 weeks to first insights) with quality (95% quote validation, idempotent scrapers, weekly automation).

**I'm transparent that this is 100-110 hours total** because:

- Production-grade requirements (idempotency, monitoring, 95% validation)
- Scale complexity (25 products, 12,500+ reviews)
- Quote anchoring validation (non-trivial string matching)
- Controlled vocabulary iteration
- Weekly automation with error handling

**However, Phase 1 MVP (65-75 hours) delivers immediate value** and proves the concept before committing to full scale.

Let's discuss which approach makes most sense for your needs.
