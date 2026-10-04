# Weekly source checks — Calvin Loves Happy Hour

## What this adds
A read-only weekly checker for the current directory. The public website and Vercel static deployment settings are unchanged.

- Reads the links already attached to the app's venue listings and checks each distinct URL once.
- Compares extractable source text against a comparison baseline you explicitly accept.
- Produces a review report, snapshots and a GitHub Actions summary.
- Creates or updates one GitHub review issue by default. No external email service is required; GitHub notifications depend on your repository/issue notification settings.
- Flags changed text, missing pages, access challenges, robots restrictions, possible closure wording, and pages requiring manual extraction.
- Does not automatically change hours, offers, prices, published verification dates, directory membership, or the accepted baseline.

**A successful fetch or unchanged page is not proof that an offer is still valid. The checker does not independently discover new venues or guarantee live business status.** Guide-only leads remain unconfirmed.

## 1. Add the package to your repository
Unzip `calvin-loves-happy-hour-weekly-checks.zip` and add its contents at your existing GitHub repository's root. Keep the folder structure intact.

Important new files and folders:

```text
.github/workflows/weekly-source-check.yml
.source-check/approved-snapshots.json
scripts/check_sources.py
scripts/accept_source_baseline.py
scripts/publish_check_issue.py
tests/test_source_checker.py
requirements-checker.txt
.gitignore
WEEKLY-CHECKS.md
```

The package also includes the unchanged app in `public/index.html`, the Vercel configuration, and the venue registry. If you have edited your live app since this package was produced, KEEP YOUR NEWER `public/index.html`. The checker reads its embedded `venue-data` array at runtime. Do not overwrite later work with the bundled copy.

Files/folders beginning with a dot may be hidden by your computer. `.github` and `.source-check` MUST be committed; simply uploading the visible files is not enough. Git is the most reliable upload route. If using the GitHub web editor, create/upload the workflow at its exact path.

If you already have a `.gitignore`, merge the new ignore entries rather than blindly replacing your existing file. Do not commit downloaded `reports/` snapshots as public app assets.

Commit the workflow onto your repository's default branch. The scheduled job is not activated by uploading files to Vercel alone.

## 2. Enable the first run
1. Open your repository's **Actions** tab.
2. If GitHub prompts you to allow workflows, enable them. Repository/organization policy may require an administrator.
3. Select **Weekly happy-hour source check**.
4. Choose **Run workflow** on the default branch.
5. Open the completed run. Read the job summary and download the `happy-hour-source-check-<run number>` artifact. It contains `weekly-report.md` and `snapshots.json`.
6. Check the repository's Issues tab for **Weekly source checks — Calvin Loves Happy Hour**.

If issue publication is blocked or Issues are disabled, the report artifact remains available. Issue publication is allowed to fail without discarding a successful check. The checker itself and its tests still must succeed.

The weekly trigger is Fridays at **12:23 UTC**: **7:23 AM CDT / 6:23 AM CST**. This is an intentionally fixed UTC schedule. Change the cron expression in the workflow if you prefer another time. Scheduled runs can be delayed; public repositories may disable schedules after extended inactivity. Check the Actions tab periodically rather than treating an expected run time as a guarantee.

The shipped package is NOT enabled in your account yet; it runs only after you commit and enable it there.

## 3. Establish the first comparison baseline
The initial baseline is deliberately empty. The first successful checks say **baseline needed**, not **verified**.

On your computer, with Python 3.12 or later installed, open a terminal in the repository folder. Download and unzip the run artifact into a local `reports` folder. Install PDF support if running the checker locally:

```bash
python -m pip install -r requirements-checker.txt
```

After reviewing the first report, accept the readable source snapshots as a starting comparison point:

```bash
python scripts/accept_source_baseline.py \
  --snapshots reports/snapshots.json \
  --all-readable \
  --acknowledge-not-offer-verification
```

Then commit and push `.source-check/approved-snapshots.json` to the default branch. Do NOT put `reports/snapshots.json` in the public website folder.

This establishes what page text to compare next week. It does NOT certify that the app's prices and hours match that text. Manually check offers against official menus before marking any listing verified. Unavailable, unreadable, deferred, and closure-flagged sources are skipped; existing fingerprints remain intact.

## 4. Review weekly changes
The next run compares against the accepted baseline in the repository.

| Result | Meaning | Your next step |
|---|---|---|
| Changed | Extracted source text differs from the accepted snapshot | Inspect the difference and the live menu. A normal menu, event, footer or navigation change may be irrelevant to happy hour. |
| Unchanged | Extracted text matches | No text change detected; this is not confirmation that offers remain valid. Images/dynamic content may still have changed. |
| Baseline needed | Readable source has no accepted fingerprint | Review it and establish its comparison baseline. |
| Unavailable | HTTP retrieval failed | Check the page manually; never infer closure from a broken link alone. |
| Manual review | Blocked, image/scanned/dynamic/oversize/encrypted content, unknown robots policy, redirect, or time-budget deferral | Inspect manually or replace the source URL with an appropriate official accessible page. |

The report includes two distinct concepts:
- **Attempt time:** a check was attempted, possibly unsuccessfully.
- **Successful source check:** readable text was actually extracted.

There is NO automatically advanced offer-verification date. Existing public source-check date labels remain unchanged by the workflow. If you later want public freshness indicators, add separately labeled attempted/source-checked/offer-reviewed fields rather than reusing one date for all three.

If a price/hour really changes:
1. Edit the venue JSON embedded in `public/index.html`.
2. Check its displayed `schedule`, numeric `sessions`, offer text, source links, quality classification and caveats together.
3. Commit the app change and review the Vercel deployment.
4. Accept the reviewed source snapshot so subsequent checks compare with the new baseline.

To accept only a selected reviewed source, instead of every readable source:

```bash
python scripts/accept_source_baseline.py \
  --snapshots reports/snapshots.json \
  --url https://tacobuddha.com/ \
  --acknowledge-not-offer-verification
```

Repeat `--url` for additional reviewed sources, then commit the updated baseline. Changed sources continue to be flagged until you accept them; they are not silently treated as unchanged next week.

Do not accept a possible closure automatically. Confirm its meaning and manually remove/update the appropriate venue or URL first. Closure wording can refer to a different business or a temporary/historical announcement.

## 5. Optional repository settings
In GitHub Settings → Secrets and variables → Actions → Variables:
- `SITE_OWNER_CONTACT`: optional publicly shareable contact URL or address included in the checker User-Agent. If unset, the repository URL is used for identification during Actions runs. Do not use a private email unless you are comfortable sending it to the source websites.
- `CREATE_SOURCE_CHECK_ISSUE`: set to `false` to disable issue publishing and use run artifacts only. Otherwise the workflow creates/updates the review issue.

The workflow requests read access to repository contents and write access to Issues. It does NOT request write access to the app code. The GitHub-provided workflow token is used only in the issue-publishing step, not sent to restaurant websites. No Vercel API token, AI API key, paid scraper credential, or email account is needed by this implementation.

## Limits, access and maintenance
- Automated source checks observe robots.txt, use a descriptive User-Agent, space requests, impose size/time limits, and do not bypass login, CAPTCHA or access challenges. Some sites may still prohibit automated retrieval in their terms; respect those restrictions and use manual checks.
- PDFs with extractable text are supported. Image-only menus/scans and JavaScript-rendered offers are not automatically verified. Menu asset links are pointers for review; they are not recursively crawled.
- Fingerprints cover normalized readable text, not semantic truth. There can be false positives from unrelated changes and false negatives from inaccessible content. Automatic AI-based offer extraction is NOT included.
- A source redirect to another host is blocked for manual review before following it. Update source links deliberately, then establish a new baseline.
- Reports are outside `public/` and are NOT deployed to Vercel. GitHub artifacts/issues follow your repository visibility. A public repository's reports, issue and committed baseline may be publicly visible. Use a private repository if you prefer private maintenance records.
- Workflow artifacts are configured for 90-day retention; GitHub account/organization limits may cap this. GitHub Actions billing and usage limits depend on your plan. No promise of unlimited free runs is made.
- Each run has an 18-minute source-check budget inside a 25-minute job limit. Deferred checks are visibly marked; a partial run is never labeled a complete verification.
- Monitor dependency updates and GitHub action-version changes. Pin approved action revisions if your security policy requires it.
- The packaged app's hosting/geocoding and device-testing caveats remain unchanged.

## Validation performed for this package
Nineteen automated offline tests passed, covering discovery/deduplication, text and PDF extraction, changed/unchanged content, missing sources, challenges, incomplete/image menus, guide classification, closure flags, robot restrictions, public-address guards, redirect guards, escaping, baseline integrity and read-only app behavior.

A fixture-based end-to-end report-generation check was also performed. These are NOT successful live checks of restaurant sources. The workflow has not been executed in your GitHub account and no scheduled run or issue has been activated for you.
