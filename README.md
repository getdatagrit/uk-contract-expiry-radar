# UK Contract Expiry Radar - Recompete Leads

UK public contracts ending soon with incumbent supplier, buyer, value and contact - recompete leads from Contracts Finder award notices.

[![Run on Apify](https://img.shields.io/badge/Run%20on-Apify-0f9f74)](https://apify.com/datagrit/uk-contract-expiry-radar) [![Docs](https://img.shields.io/badge/docs-getdatagrit.github.io-0e1726)](https://getdatagrit.github.io/uk-contract-expiry-radar/)

**from $3.50 per 1,000 results + $10 per run (pay per result; the rate depends on your Apify plan).** Export as JSON, CSV or Excel, call it through the API, or schedule it on Apify.

## What it does

UK Contract Expiry Radar turns Contracts Finder award notices (the UK government portal for contracts, without Find a Tender) into a list of public contracts that are about to end. For every contract it returns the incumbent supplier (with Companies House number), the buying organisation, the award value, the contract start and end dates, and how many days are left. Filter by expiry window, CPV code, keyword and value, then export the result as JSON, CSV or Excel, call it through the Apify API, or plug it into n8n, Make and AI agents through MCP.

## Quick start

1. Open [UK Contract Expiry Radar - Recompete Leads on Apify Store](https://apify.com/datagrit/uk-contract-expiry-radar) and click **Try for free**.
2. Fill in the input form (or paste the JSON below) and run it.
3. Download the dataset, or fetch it from the API.

```json
{
  "expiresAfterMonths": 0,
  "expiresBeforeMonths": 12,
  "publishedLookbackDays": 7,
  "maxItems": 20
}
```

## Input

| Field | Type | What it does |
|---|---|---|
| `queries` | array | Optional. A contract is returned when its title, award and tender descriptions, buyer or supplier name contains any of these keywords (case-insensitive; the description is searched in full even though the result shows it shortened to 800 characters). Leave empty to list every contract in the expiry window. Keywords narrow results but make the scan read more notices. |
| `expiresAfterMonths` | integer | Earliest contract end date, counted in months from today. 0 includes contracts that end from today on; use 6 to skip contracts ending in the next six months. |
| `expiresBeforeMonths` | integer | Latest contract end date, counted in months from today. 12 lists contracts that come up for recompete within a year. |
| `publishedLookbackDays` | integer | How far back to read Contracts Finder award notices, newest first; the scan stops as soon as Maximum results is reached. Roughly one to two hundred award notices are published per working day (1 to 2 API requests; very few at weekends) and the source allows 12 requests per 120 seconds (the Actor paces itself), so the default 7 days (11 to 12 requests, about 15 seconds) normally never waits, and each further working day of lookback adds roughly 20 seconds of pause. Long contracts (3-5 years) need a long window to appear as expiring - raise this together with Maximum run time. |
| `maxRunSeconds` | integer | Hard time budget for reading the source. When it is reached the Actor stops, keeps every contract found so far and says how many lookback days it covered in the run status. 0 removes the limit (needed for long lookback windows with strict filters). |
| `cpvPrefixes` | array | Optional. Keep only contracts whose CPV code (main or additional) starts with one of these prefixes, for example 72 for IT services or 45 for construction. |
| `minValue` | integer | Skip contracts with an awarded value below this amount. Awards published without a value (the source reports 0) are skipped as soon as a minimum above 0 is set. 0 keeps everything, including awards without a value. |
| `includeFrameworkCallOffs` | boolean | Call-offs from frameworks such as G-Cloud and from dynamic purchasing systems are usually short and re-bought through the same route. Turn off to drop every notice whose procurement procedure says call-off from a framework agreement or a dynamic purchasing system. Notices that only mention call-off in their text are kept. |
| `includeContactEmail` | boolean | Adds the contact email the buyer published on the notice. Off by default; when you turn it on, you are responsible for using it in line with data protection rules. |
| `maxItems` | integer | Stop after this many contracts. |
| `proxyConfiguration` | object | Optional proxy. Contracts Finder is a public government API and normally needs none; enable Apify Proxy only if the run fails with a message that the source is throttling your IP address. |

## Output

| Field | Type | Description |
|---|---|---|
| `query` | string | The keyword from your input that matched this contract; null when you used no keywords. |
| `id` | string | Stable identifier of the award (OCDS award id). One notice can hold several awards. Empty only on the single status row emitted when nothing matched. |
| `ocid` | string | Open Contracting ID of the whole procurement process, shared by all its notices. |
| `title` | string | Title of the contract as published by the buyer. Empty only on the single status row emitted when nothing matched. |
| `description` | string | Contract description: the award description and, when the notice also publishes a different tender description, that text after it. Whitespace collapsed, cut at 800 characters. |
| `sourceUrl` | string | Link to the award notice on Contracts Finder. |
| `buyerName` | string | Public body that awarded the contract, i.e. who will buy again. |
| `buyerId` | string | Contracts Finder identifier of the buyer organisation. |
| `buyerLocality` | string | Town or city of the buyer. |
| `buyerPostcode` | string | Postcode of the buyer. |
| `buyerWebsite` | string | Website of the buyer when published. |
| `buyerContactEmail` | string | Contact email the buyer published on the notice. Filled only when you turn on 'Include buyer contact email'. |
| `supplierName` | string | Supplier that won the contract (first supplier when there are several). |
| `supplierCount` | integer | Number of suppliers awarded on this award, more than 1 for multi-supplier frameworks. |
| `allSuppliers` | string | Names of all suppliers separated by semicolons; null when there is a single supplier. |
| `supplierCompanyNumber` | string | Companies House number of the first supplier when the notice gives one. |
| `supplierScale` | string | Size class of the first supplier as declared on the notice: sme or large. |
| `supplierVcse` | boolean | Whether the first supplier is a voluntary, community or social enterprise; null when not stated. |
| `awardValue` | number | Awarded contract value in the currency field, as published; null when the buyer did not disclose a value (the source publishes 0 in that case). |
| `currency` | string | Currency of the award value. |
| `awardDate` | string | Date the contract was awarded, ISO 8601. |
| `publishedAt` | string | When the award notice was published on Contracts Finder, ISO 8601. |
| `contractStart` | string | Start of the contract period, ISO 8601. |
| `contractEnd` | string | End of the contract period as published, ISO 8601. This is the date the recompete is driven by. |
| `daysToExpiry` | integer | Whole days from the run date to the contract end; negative when the contract has ended. |
| `monthsToExpiry` | number | Months from the run date to the contract end, one decimal. |
| `contractLengthMonths` | integer | Length of the contract period in months. |
| `cpvCode` | string | Main Common Procurement Vocabulary code of the contract. |
| `cpvDescription` | string | Plain-language description of the main CPV code. |
| `additionalCpvCodes` | string | Further CPV codes on the notice, comma separated. |
| `category` | string | Main procurement category: goods, services or works. |
| `procurementMethod` | string | OCDS procurement method: open, selective, limited or direct. |
| `procurementMethodDetails` | string | Procedure as described by the buyer, for example Restricted procedure or Call-off from a framework agreement. |
| `frameworkCallOff` | boolean | True when the procurement procedure field of the notice says call-off from a framework agreement or from a dynamic purchasing system. Based only on that field, not on the wording of the title or description. |
| `callOffMentioned` | boolean | True when the title or description contains the words call-off. It is a mention in the text, not a statement of the procedure: use frameworkCallOff for the procedure. |
| `extensionMentioned` | boolean | True when the title or description mentions an extension, renewal or optional period, so the real end date may be later. |
| `smeSuitable` | boolean | Whether the buyer flagged the contract as suitable for small and medium enterprises; null when not stated. |
| `recordDisputed` | boolean | True when the award was published more than once and the copies disagree on a value (both non-empty and different). The row carries one of them; the other is in disputedOtherValues. A missing value (0, null) in one copy is filled from the other and is not a dispute. |
| `disputedFields` | string | Comma separated names of the fields the duplicate copies disagree on; null when recordDisputed is false. |
| `disputedOtherValues` | string | The values of the disputed fields that were not used in this row, as field=value pairs separated by semicolons; null when recordDisputed is false. |
| `found` | boolean | False only for the single status row emitted when nothing matched your filters (not billed). |
| `scrapedAt` | string | ISO 8601 timestamp of the run. |

Sample record:

```json
{
  "query": "software",
  "id": "ocds-b5fd17-82384ae3-342e-49e5-aba3-f07a7b4a2787-1",
  "ocid": "ocds-b5fd17-82384ae3-342e-49e5-aba3-f07a7b4a2787",
  "title": "UOW995b - Virtual Placement Software - AWARD",
  "description": "The University of Worcester is seeking to procure a Virtual Placement Simulation Software Platform.",
  "sourceUrl": "https://www.contractsfinder.service.gov.uk/Notice/1fcc1776-84a0-4171-9c5b-f245323ded52",
  "buyerName": "University Of Worcester",
  "buyerId": "GB-CFS-44290",
  "buyerLocality": "Worcester",
  "buyerPostcode": "WR2 6AJ",
  "buyerWebsite": "https://www.worcester.ac.uk/",
  "buyerContactEmail": "procurement@example.org.uk",
  "supplierName": "Insight Direct (UK) Ltd",
  "supplierCount": 1,
  "allSuppliers": "Alpha Ltd; Beta Ltd",
  "supplierCompanyNumber": "02343760",
  "supplierScale": "sme",
  "supplierVcse": false,
  "awardValue": 318979.29,
  "currency": "GBP",
  "awardDate": "2026-08-20T23:00:00.000Z",
  "publishedAt": "2026-09-23T12:09:20.000Z",
  "contractStart": "2026-09-30T23:00:00.000Z",
  "contractEnd": "2029-09-30T22:59:59.000Z",
  "daysToExpiry": 1096,
  "monthsToExpiry": 36,
  "contractLengthMonths": 36,
  "cpvCode": "48100000",
  "cpvDescription": "Industry specific software package",
  "additionalCpvCodes": "48000000, 72000000",
  "category": "services",
  "procurementMethod": "selective",
  "procurementMethodDetails": "Restricted procedure",
  "frameworkCallOff": false,
  "callOffMentioned": false,
  "extensionMentioned": false,
  "smeSuitable": false,
  "recordDisputed": false,
  "disputedFields": "awardValue",
  "disputedOtherValues": "awardValue=504113",
  "found": true,
  "scrapedAt": "2026-09-30T00:00:00.000Z"
}
```

## Call it from code

Runnable examples are in [`examples/`](examples). Replace `YOUR_APIFY_TOKEN` with the token from your Apify account settings.

```bash
curl -X POST "https://api.apify.com/v2/acts/datagrit~uk-contract-expiry-radar/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"expiresAfterMonths":0,"expiresBeforeMonths":12,"publishedLookbackDays":7,"maxItems":20}'
```

## FAQ

**What if the run returns nothing?**  
The run status tells you why. "Published no award notices in the last N days" means the source had nothing in the window (it is nearly empty at weekends, so a 1-day lookback on a Saturday or Sunday is often empty): raise the lookback days. If the source answers with empty days on working days while it is clearly throttling (it does this when one IP sends many requests), the Actor waits up to 3.5 minutes per day (within "Maximum run time"), re-checking against a control request, and then either continues without that day (named in the run status) or, if nothing could be read at all, fails with a clear message instead of returning an empty result: run it again later or turn on a proxy. "No contract matched your filters" means notices were read but none passed your expiry window, keywords, CPV or value filters: widen those.

**Does it list contracts that are not yet awarded?**  
No. It covers awarded contracts and shows when each one ends. Use the expiry window to find recompete opportunities early.

**Why do some contracts have no end date?**  
Some notices are published without a contract period. Those are skipped, because the expiry window cannot be applied.

**How current is the data?**  
Every run reads the live Contracts Finder API for the lookback window you choose.

**How long does a run take?**  
Contracts Finder allows 12 requests per 120 seconds. A working day costs 1 or 2 requests (a day with more than 100 notices needs a second page), a weekend day 1. The default 7-day window comes to 11 to 12 requests (measured on the live feed: 11 or 12 requests and several hundred award records per week; the exact number changes from week to week), which fits inside the limit: reading the whole week takes about 15 seconds with no pause. The count is not fixed: a single day above 200 notices (the busiest measured day had 193) would add a request, and the 13th request waits about two minutes for the window to reopen - the run then takes around 2.5 minutes, still inside the default 240-second budget. Windows longer than a week need more than 12 requests, and then the Actor pauses about two minutes after every further 12 requests (roughly 20 seconds per extra working day). The run also stops at the run-time budget (240 seconds by default): the results found so far are kept and the run status says how many lookback days were read. For a longer lookback window raise both the lookback days and the maximum run time (0 removes the limit).

**Which sources does it cover?**  
Contracts Finder only. Contracts published only on Find a Tender (the above-threshold notices) are not included, and some other UK tender Actors on the Store read both portals. The nearest Actor on the same source, dataio/uk-public-contract-awards, also reads only Contracts Finder and filters by the days left to contract end, so the expiry view is not what sets this Actor apart. Choose this Actor when you care about how the award rows are built: a call-off flag taken from the procurement procedure field (`frameworkCallOff`) next to a separate flag for call-off wording in the text (`callOffMentioned`), an extension-clause flag (`extensionMentioned`), one row per award with corrected and republished notices merged field by field and conflicting values marked (`recordDisputed`), and an expiry window counted in months ahead.

**What if a notice was corrected and published twice?**  
You get one row per award, merged field by field. On the live feed the two copies of a duplicated award carry the same award date (12 of 12 pairs in a 7-day scan), so the award date cannot tell which copy is right. The rules are: - A missing value never beats a real one. When one copy has no award value (the source publishes 0, shown as null), no end date or no supplier and the other has the data, the row carries the data (in the scan above one award was published once with a value of 0 and once with 150,263.40 GBP; the row shows 150,263.40). Days, months to expiry and contract length are recomputed from the merged dates. - When both copies have a value and the values differ (for example an award value of 504,113 against 50,411,351, or an end date a year apart), the Actor cannot know which is right. It keeps the copy with the later award date, or the later one in the feed when the dates are equal, and marks the row: `recordDisputed` is true, `disputedFields` lists the fields and `disputedOtherValues` shows the values it did not use (for example `awardValue=504113`). Check those rows against the notice link before you rely on the value; in the 7-day scan 5 of 586 rows were disputed. - `frameworkCallOff` is recomputed from the procedure of the kept copy; `callOffMentioned` and `extensionMentioned` are true when either copy says so. A newer publication day always wins over an older one.

**Can I schedule it?**  
Yes, use Apify schedules or call the Actor from your own workflow.

**Something looks wrong.**  
Open an issue with the input you used; layout or API changes at the source are fixed quickly.

## More from datagrit

- [TED Contract Expiry Radar - Recompete Leads](https://github.com/getdatagrit/ted-contract-expiry-radar) - Find EU public contracts approaching expiry from TED award notices: incumbent, buyer, value, end date and renewal options.
- [French Company Finder - Sirene Financials](https://github.com/getdatagrit/french-company-finder) - French company lead lists from Sirene screened by net result and revenue, with net margin, size, matching establishment and optional directors.
- [GLEIF LEI Lookup - Parents And Subsidiaries](https://github.com/getdatagrit/gleif-lei-ownership-tree) - GLEIF legal entity records with direct and ultimate parents, reporting exceptions and direct subsidiaries.
- [IRS 990 Nonprofit Officers and Compensation](https://github.com/getdatagrit/irs-990-officer-compensation) - Named officers, directors and key employees with pay, hours and titles from IRS e-filed 990, 990-EZ and 990-PF returns.
- [Poland KRS New Company Registrations Feed](https://github.com/getdatagrit/poland-krs-new-companies) - Newly registered Polish companies, foundations and associations from the official KRS court register: NIP, address, PKD, capital, email, with filters and change detection.

All Actors: [https://getdatagrit.github.io/](https://getdatagrit.github.io/) · [Apify Store](https://apify.com/datagrit)

---

This repository holds documentation and usage examples. Questions, bug reports and feature requests: use the **Issues** tab of the Actor page on [Apify Store](https://apify.com/datagrit/uk-contract-expiry-radar). Examples are MIT licensed.
