# How to Scrape Computrabajo Job Postings (18 Latin American Countries, No Login)

> **Run it now, no code: [computrabajo-jobs-scraper on the Apify Store](https://apify.com/bibian/computrabajo-jobs-scraper)** &nbsp;|&nbsp; $0.90 per 1,000 jobs &nbsp;|&nbsp; no account or login needed

Computrabajo is the largest job board network in Latin America, with a separate site for 18 countries from Argentina to Puerto Rico. It has no public export and no official API for third parties. This guide shows how to get Computrabajo job postings across any of its 18 country sites as clean structured data: title, company, salary, location, employment type and full description, with a free Apify Actor, from the console or from code.

Searches this page answers: computrabajo scraper, computrabajo job postings api, latin america job board scraper, computrabajo salary data.

## What you get from the Computrabajo scraper

- Every job posting matching your country and keywords, with title, employment type and hiring company.
- Salary range (`salaryMin`, `salaryMax`, `salaryCurrency`, `salaryPeriod`) when the employer publishes one.
- Hiring company name, industry and office location (city, region, country).
- Full job description as plain text, with posting and expiry dates.

Export the dataset as JSON, CSV or Excel, or read it through the Apify API. A real sample (five results) is in [`sample-output.json`](sample-output.json).

## Output fields

| Field | What it is |
|---|---|
| `id`, `url` | Computrabajo job id and public link |
| `title` | Job title |
| `companyName`, `industry` | Hiring company and its industry |
| `city`, `region`, `country` | Office location |
| `salaryMin`, `salaryMax`, `salaryCurrency`, `salaryPeriod` | Salary range, when published |
| `employmentType` | Contract type (`FULL_TIME`, `PART_TIME`...) |
| `datePosted`, `validThrough` | Publication date and expiry date |
| `description` | Plain text job description |

## Use cases

- **Monitor a Latin American job market.** New postings by job title and country, from Argentina to Puerto Rico.
- **Feed a job alert or an aggregator.** Run it daily and dedupe by `id` to catch only the new postings.
- **Find companies that are hiring.** See which employers recruit for a given role right now, in which city.
- **Benchmark salaries.** Published salary ranges by role and country, where the employer discloses them.

## Input

| Field | What it does |
|---|---|
| `country` | Which Computrabajo country site to search (Argentina, Mexico, Colombia, Chile and 14 more). |
| `keywords` | Job title or skill, for example `vendedor` or `contador`. Empty rotates through common job titles. |
| `maxItems` | The run stops once this many postings are saved (default 50). |
| `includeDescriptions` | Adds the full job description as plain text. |

Example input:

```json
{
  "country": "ar",
  "keywords": "",
  "maxItems": 50,
  "includeDescriptions": true
}
```

## Three ways to use it

### 1. Apify console, no code

1. Open [the Actor page](https://apify.com/bibian/computrabajo-jobs-scraper) and click **Try for free** (a free Apify account is enough).
2. Pick a country, fill in your keywords and click **Start**.
3. When the run finishes, open the **Output** tab, check the table, then **Export** as CSV, Excel or JSON.
4. For a daily alert, open **Schedules**, create a daily schedule for the Actor and, optionally, add an integration that sends new postings to Slack, e-mail or Google Sheets.

### 2. Apify API with JavaScript

```bash
npm install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
node examples/run.js
```

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });

const run = await client.actor('bibian/computrabajo-jobs-scraper').call({
    "country": "ar",
    "keywords": "",
    "maxItems": 50,
    "includeDescriptions": true
});

const { items } = await client.dataset(run.defaultDatasetId).listItems();
for (const item of items) {
    console.log(`${item.title} | ${item.companyName} | ${item.city}, ${item.country} | ${item.salaryMin ?? '-'}-${item.salaryMax ?? '-'} ${item.salaryCurrency ?? ''}`);
}
```

The complete file is [`examples/run.js`](examples/run.js) (ES module, Node 18 or later).

### 3. Apify API with Python

```bash
pip install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
python examples/run.py
```

```python
import os
from apify_client import ApifyClient

client = ApifyClient(os.environ["APIFY_TOKEN"])

run = client.actor("bibian/computrabajo-jobs-scraper").call(run_input={
    "country": "ar",
    "keywords": "",
    "maxItems": 50,
    "includeDescriptions": True,
})

for item in client.dataset(run.default_dataset_id).iterate_items():
    print(f"{item['title']} | {item.get('companyName')} | {item.get('city')}, {item.get('country')} | {item.get('salaryMin')}-{item.get('salaryMax')} {item.get('salaryCurrency') or ''}")
```

The complete file is [`examples/run.py`](examples/run.py). Never put your token in the code: read it from the `APIFY_TOKEN` environment variable as above.

## Pricing

Pay per result: **$0.90 per 1,000 jobs** (that is $0.0009 per job posting), plus a tiny start fee of $0.00005 per run. You pay only for the results saved to the dataset. Apify's free plan includes monthly credits, enough to try it on a few hundred results. Prices are those shown on the [Store page](https://apify.com/bibian/computrabajo-jobs-scraper) when this guide was written; the Store page is the reference.

## Limits and honest notes

- Results follow Computrabajo's own search, so some postings from nearby cities or related titles can appear.
- Salary fields are empty when the employer does not publish a figure (common on this site).
- Only the hiring company and office location are collected: no recruiter or employee personal data.
- No browser and no proxy needed for most requests: runs are fast and cheap.
- This is an unofficial tool, not affiliated with Computrabajo. Check the site terms and the data protection rules that apply to your use.

## FAQ

**Does it need a browser or a proxy?**  
No for most runs: every page used is a plain HTTP request. Under heavy volume the Actor escalates automatically to a proxy, which is already included in the price.

**How do I only get new postings every day?**  
Schedule the Actor once a day with your usual country and keywords. Results come newest first, so duplicates across runs are easy to filter out by `id`.

**Does it collect recruiter contact details?**  
No. Only the hiring company's public details (name, industry) and the office location are collected, never an individual's data. A line that carries a recruiter's e-mail address is dropped from the description before it is saved.

## Try it

**[Open computrabajo-jobs-scraper on the Apify Store](https://apify.com/bibian/computrabajo-jobs-scraper)**

The code in `examples/` is released under the MIT license (see [LICENSE](LICENSE)). The data belongs to its original publishers.
