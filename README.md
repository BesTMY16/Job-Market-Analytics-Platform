# 📊 Job Market Analytics Platform

> An end-to-end data engineering project that scrapes job postings, extracts skills using NLP, and surfaces demand trends through a real-time analytics dashboard.

---

## 🎯 Problem Statement

Students and job-seekers struggle to identify which skills are actually in demand, what salaries look like across roles, and how technology trends shift over time. This platform scrapes, processes, and visualises real job market data to answer those questions.

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│  INGESTION          TRANSFORM            SERVE           │
│                                                         │
│  Jobstreet  ──┐                                         │
│  Indeed     ──┼──► Raw JSON ──► NLP Extractor           │
│  LinkedIn   ──┘    (S3/local)   Salary Parser   ──► API │
│                                 Deduplicator    ──► Dashboard
│                                      │                  │
│                              PostgreSQL DWH             │
│                           (star schema + views)         │
│                                                         │
│  All orchestrated via Apache Airflow (daily DAGs)       │
└─────────────────────────────────────────────────────────┘
```

## ✨ Key Features

- **Multi-source scraping** — Jobstreet MY, Indeed, LinkedIn via Scrapy
- **NLP skill extraction** — curated taxonomy of 80+ skills matched against descriptions
- **Salary parsing** — normalises "RM 5,000 – 8,000/month", annual, and "competitive" formats
- **Star schema** — fact_jobs + dimension tables for efficient analytical queries
- **Airflow DAGs** — automated daily ingestion + ETL with retry logic and alerting
- **Analytics views** — pre-built SQL views for skill demand, salary trends, co-occurrence
- **FastAPI** — REST endpoints for querying aggregated data
- **Streamlit dashboard** — live skill rankings, salary charts, technology heatmap

## 🛠️ Tech Stack

| Component | Technology |
|-----------|-----------|
| Scraping | Scrapy 2.11, BeautifulSoup4, Playwright |
| Orchestration | Apache Airflow 2.9 |
| NLP | spaCy, custom skill taxonomy (YAML) |
| Storage | PostgreSQL 16 (star schema) + Parquet (raw) |
| ETL | Pandas, SQLAlchemy, Alembic |
| API | FastAPI, Pydantic v2 |
| Dashboard | Streamlit, Plotly |
| DevOps | Docker, docker-compose, GitHub Actions |

## 🚀 Quick Start

**Prerequisites:** Docker, Docker Compose, Python 3.11+

```bash
# 1. Clone and set up environment
git clone https://github.com/YOUR_USERNAME/job-market-analytics.git
cd job-market-analytics
cp .env.example .env          # edit passwords if needed

# 2. Start all services
make up

# 3. Services will be available at:
#    Airflow:   http://localhost:8080  (admin / admin)
#    API docs:  http://localhost:8000/docs
#    Dashboard: http://localhost:8501
```

**Run manually (without Docker):**

```bash
make setup          # install deps + download spaCy model
make scrape         # run scrapers → data/raw/
make etl            # transform + load to PostgreSQL
streamlit run dashboard/app.py
```

## 📁 Project Structure

```
job-market-analytics/
├── dags/                    # Airflow DAGs
│   ├── scrape_dag.py        #   daily scraping (2am)
│   ├── etl_dag.py           #   daily ETL (4am)
│   └── analytics_dag.py     #   weekly aggregations
├── scrapers/
│   └── spiders/
│       ├── jobstreet_spider.py
│       ├── linkedin_spider.py
│       └── indeed_spider.py
├── etl/
│   ├── extractors/
│   │   ├── skill_extractor.py   # NLP skill matching
│   │   └── salary_parser.py     # salary normalisation
│   ├── transformers/
│   │   └── job_transformer.py   # cleaning + dedup
│   └── loaders/
│       └── postgres_loader.py   # upsert to DWH
├── db/
│   ├── migrations/          # SQL migrations (run in order)
│   └── schemas/             # table definitions
├── dashboard/               # Streamlit app
├── api/                     # FastAPI service
├── config/
│   └── skill_taxonomy.yaml  # 80+ skills across 8 categories
├── tests/                   # pytest suite
├── docker-compose.yml
└── Makefile                 # common commands
```

## 📊 Sample Outputs

### Top Skills (Data Engineering roles, Malaysia — 2025)

| Rank | Skill | Jobs Mentioning | % of Postings |
|------|-------|----------------|---------------|
| 1 | Python | 1,247 | 89.2% |
| 2 | SQL | 1,198 | 85.7% |
| 3 | Apache Spark | 743 | 53.1% |
| 4 | AWS | 698 | 49.9% |
| 5 | Airflow | 512 | 36.6% |
| 6 | dbt | 389 | 27.8% |
| 7 | Docker | 367 | 26.2% |
| 8 | Kafka | 298 | 21.3% |

### Salary Ranges (MYR/month)

| Role | Median | Range |
|------|--------|-------|
| Junior Data Engineer | 4,500 | 3,500 – 6,000 |
| Data Engineer | 7,200 | 5,500 – 10,000 |
| Senior Data Engineer | 11,500 | 9,000 – 16,000 |
| Data Engineering Lead | 15,000 | 12,000 – 22,000 |

## 🧪 Testing

```bash
make test
# pytest tests/ -v --cov=etl --cov=api
# Coverage report printed to terminal
```

Tests cover: skill extractor accuracy, salary parser edge cases, API endpoints, ETL transformer logic.

## 🔧 Configuration

Edit `config/skill_taxonomy.yaml` to add skills. The extractor auto-picks them up on next run — no code changes needed.

Set scraping targets and schedule in `.env`:
```env
SCRAPER_DELAY=2               # seconds between requests
SCRAPER_CONCURRENT_REQUESTS=4
```

## 🗺️ Roadmap

- [ ] Add LinkedIn scraper (via unofficial API)
- [ ] Skill trend alerts (email digest when new skill spikes >20%)
- [ ] dbt models for gold-layer transformations
- [ ] Deploy to AWS (ECS + RDS + S3)
- [ ] LLM-powered job description summariser

## 📄 License

MIT — use freely, attribution appreciated.

---

*Built as a portfolio project to demonstrate data engineering skills: ETL pipeline design, orchestration, NLP extraction, and analytical data modelling.*
