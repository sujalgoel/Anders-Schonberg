# Phase 1: MVP - Weekly Breakdown (55-60 hours, 3 weeks)

## Week 1: Scraper Development (18-20 hours)

### Goal: Build production-ready scraper for G2 or Capterra

**Sub-goals:**

- Set up development environment and Azure Blob Storage access
- Implement core scraper with authentication
- Build idempotency and data validation logic

**Tasks:**

- [ ] Set up Python project, Azure credentials, and dev environment
- [ ] Research G2 vs Capterra DOM structure, choose easier platform
- [ ] Implement authentication/login flow
- [ ] Build pagination handler for review pages
- [ ] Extract review data: text, rating, reviewer role, date, title
- [ ] Handle expandable content ("Read more" buttons)
- [ ] Implement idempotency logic (review_id generation, duplicate prevention)
- [ ] Add rate limiting and human-like delays
- [ ] Test scraper with 1-2 products, store JSONL to Azure Blob

**Deliverable:** Working scraper for chosen platform

---

## Week 2: LLM Extraction Pipeline (20-22 hours)

### Goal: Build LLM extraction system with quote validation

**Sub-goals:**

- Design and test extraction prompts for 8 fact types
- Implement batch processing and validation
- Co-create controlled vocabulary with client

**Tasks:**

- [ ] Set up Azure OpenAI API access (GPT-5.2)
- [ ] Design extraction prompts for 8 fact types
- [ ] Test prompts with sample reviews, iterate on quality
- [ ] Implement batch processing (10-25 reviews per call)
- [ ] Build quote anchoring validation with fuzzy matching
- [ ] Add schema validation (fact types, enums, required fields)
- [ ] Workshop controlled vocabulary with client
- [ ] Implement retry logic for failed batches
- [ ] Test extraction on 100-200 reviews, measure validation rate

**Deliverable:** LLM extraction pipeline achieving 85-90% quote validation

---

## Week 3: Database, Testing & Demo (17-18 hours)

### Goal: Complete system with queryable database and documentation

**Sub-goals:**

- Set up Azure SQL database with proper schema
- Run full extraction on 5 competitor products
- Create sample queries and documentation

**Tasks:**

- [ ] Design SQL schema for reviews and facts tables
- [ ] Set up Azure SQL Database with indexes
- [ ] Build ETL pipeline: JSONL → SQL
- [ ] Scrape all 5 products: Jira, ClickUp, Asana, Monday.com, Notion
- [ ] Run LLM extraction on ~2,500 reviews
- [ ] Build sample queries demonstrating insights
- [ ] Test quote validation pass rate, fix edge cases
- [ ] Write documentation for running the system
- [ ] Demo walkthrough with client

**Deliverable:** Complete system with 5 products, queryable database, documentation

---

## Total Hours: 55-60 hours

**Weekly Progress Tracking:**

- Week 1: Scraper working for 1 platform
- Week 2: LLM extraction with validation
- Week 3: Full system operational with demo

**Risks to monitor:**

- DOM structure changes during development
- LLM extraction quality below 85%
- Azure access delays from DevOps team

**Success Criteria:**

- ✅ Raw reviews match website text verbatim
- ✅ No duplicate reviews across runs
- ✅ Facts contain only allowed types and valid quotes
- ✅ 85-90% quote validation pass rate
- ✅ Sample queries demonstrate actionable insights
