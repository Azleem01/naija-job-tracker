# Daily Refresh Instructions - NaijaRadar (Nigeria + Remote Job Tracker)

You are a scheduled cloud agent. Your job: rebuild `index.html` in this repo with fresh Nigeria +
high-chance-remote Data / ML jobs for **Azeez Aleem** (Data Scientist / ML Engineer, junior-mid), then
commit & push so GitHub Pages redeploys. Work only inside this repo. Keep the entire design (all CSS,
the SVG icon set, the JS logic) unchanged. **Only** replace the `const JOBS = [ ... ];` array and the
`const LAST_UPDATED = "...";` line. Do NOT modify the `CV_BASE` object or the `buildCV` / `downloadCV`
functions. **Never use em dashes and never add emojis** anywhere (the design is deliberately emoji-free
and uses inline SVG icons only).

## 0. Candidate profile (for scoring + keyword gaps)
CV skills he ALREADY has: Python, TypeScript, SQL, PyTorch, TensorFlow, scikit-learn, XGBoost, Pandas,
NumPy, FastAPI, REST APIs, React, Next.js, Tailwind, Git/GitHub, LLM application dev, Adversarial ML,
OCR, Data Analysis & Visualization. First-Class EEE degree; MSc Control & Optimization, Imperial College
London. He is based in Nigeria. Targets: junior / mid / graduate / early-career. Wants roles he has a
HIGH chance of landing.

## 1. Collect jobs (6 sources)
For EACH of the 4 role tracks (Data Scientist, Machine Learning / AI Engineer, Data Analyst,
AI Trainer / LLM), collect listings (title, company, location, posted date, salary if shown, detail URL).

**Indeed Nigeria (use the Indeed MCP tool `search_jobs` FIRST if available):** `country_code:"NG"`,
once with `location:"Lagos"` and once with `location:"remote"` per track. Keep the `to.indeed.com/...`
`View Job URL` intact as `url`, set `src:"Indeed"`. If the Indeed tool is unavailable this run, skip it.

**Web sources (use WebFetch):**
- **ng.jooble.org** (freshest; `date=3`): `https://ng.jooble.org/SearchResult?ukw=<ROLE>&date=3`
  (use precise queries like "data scientist", "data analyst", "AI"; NOT "machine learning" which
  matches "machine operator"). Includes remote roles.
- **MyJobMag**: `https://www.myjobmag.com/search/jobs?q=<ROLE>`
- **Jobberman**: `https://www.jobberman.com/jobs?q=<ROLE>`
- **HotNigerianJobs**: `https://www.hotnigerianjobs.com/`
- **Himalayas** (remote): `https://himalayas.app/jobs?search=<ROLE>` (keep only worldwide / Africa /
  Nigeria-eligible remote roles; skip US-only or UK-only).

If a source returns 403 / timeout / empty / unavailable, skip it silently and continue (it will show
"0 today" in the Sources panel).

## 2. Filter
- **Location rule:** keep ALL remote / worldwide / Africa-eligible jobs (set `remote:true`,
  `commutable:false`). For on-site jobs keep ONLY Nigeria locations (Lagos, Abuja, Port Harcourt,
  Ibadan, Ikeja, Lekki, Sangotedo, Kano, Enugu, Abeokuta, Benin City, Uyo, etc.); set `remote:false`,
  `commutable:true`. DROP on-site jobs outside Nigeria (unless they are remote).
- **Seniority:** DROP clearly senior roles (Senior, Staff, Lead, Principal, Head, Director) unless the
  role is a low-barrier remote AI-trainer/annotation gig. Keep junior/mid/graduate/associate/untitled.
- **Freshness:** prefer posted <= 2 days, but KEEP older matches too (the UI labels their age). Skip
  anything older than ~35 days.
- **Dedupe** across sources by title+company; keep the copy with a real posted date + working URL.
- Aim for ~28-35 good jobs spread across the 4 tracks.

## 3. Compute fields per job
`age` = whole days from today (UTC); "X hours ago"/"today" => 0. `remote`/`commutable` per the rule
above. `type`: Full-time / Part-time / Contract / Freelance / Graduate as stated. `salary`: keep any
shown (NGN or USD, use a hyphen for ranges, e.g. "NGN 250k-400k"); else "".

### Likelihood score (integer 0-100)  [Nigeria-tuned]
Start at 55 (he is a strong local candidate: First-Class + Imperial MSc), then:
- +22 junior/graduate/entry/early-career; +10 mid-level
- +3 per CV skill the job clearly wants (cap +22)
- +10 part-time/contract/freelance; +8 remote; +12 AI-trainer/annotation/LLM-labelling (low barrier)
- +6 Nigeria on-site (he is based there; easy to attend/interview)
- -15 heavy mismatch (pure data-engineering/DevOps) or "PhD required" / "5+ yrs"
- -25 senior/lead/principal
Clamp 0-100. UI derives band: >=70 High, 45-69 Medium, <45 Low.

### Keywords
- `have`: 3-4 CV skills this job values.
- `add`: 2-3 in-demand skills the job wants that are NOT on his CV. Nigeria pool commonly includes:
  Power BI, Tableau, Excel, SQL, dbt, Looker, Docker, AWS/GCP/Azure, MLOps, Airflow, Spark, LangChain,
  RAG, Prompt engineering, Annotation, A/B testing, Statistics, Credit risk, Fraud analytics.

## 4. Job object schema (match the existing file exactly)
```js
{t:"Title",co:"Company",loc:"Lagos",cat:"ds|mle|da|ai",remote:false,commutable:true,
 type:"Full-time",posted:"2 days ago",age:2,salary:"" or "NGN 250k-400k",
 src:"Indeed|Jooble|MyJobMag|Jobberman|HotNigerianJobs|Himalayas",
 url:"https://...direct link...",like:78,have:["SQL","Python"],add:["Power BI","dbt"]}
```
`cat`: Data Scientist->`ds`, ML/AI Engineer->`mle`, Data Analyst->`da`, AI Trainer/LLM->`ai`.
`src` for ng.jooble listings = "Jooble". Keep every `url` intact (do not strip params).

## 5. Update index.html
- Replace the whole `const JOBS = [ ... ];` block with the new array.
- Set `const LAST_UPDATED = "<D Mon YYYY>";` to today.
- Leave `SOURCES`, `IC` (icons), all CSS, and all functions unchanged. No emojis, no em dashes.
- Sanity-check the file still parses (balanced brackets) and tab counts add up.

## 6. Commit & push
```
git add index.html
git -c user.email="telaleem01@gmail.com" -c user.name="Azeez Aleem" commit -m "Daily refresh: Nigeria + remote jobs $(date -u +%Y-%m-%d)"
git push origin main
```
GitHub Pages redeploys automatically within ~1 minute -> https://azleem01.github.io/naija-job-tracker/
