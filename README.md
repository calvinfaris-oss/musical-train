# Calvin Loves Happy Hour — St. Louis happy-hour pilot

## What you have
A mobile-friendly, static web app. No build step, account system, secret keys, or paid database is required for this version. It asks for a US location and provides optional browser geolocation, filters, saved venues, venue maps, official menu links, and approximate distances.

This is a small MVP, not a nationwide directory. Seventy entries are included: 47 with official-page support and 23 opt-in guide-only leads. This is a researched expansion, not all venues in the area. Websites were checked October 1, 2026. Nothing refreshes automatically. Bowood's published hours conflict, so its listing is marked for confirmation and excluded from the live-time filter. No restaurant photos or logos are reused.

## Launch on Vercel — recommended GitHub route
1. Download and unzip calvin-loves-happy-hour-vercel.zip.
2. Create a GitHub repository and upload the extracted files, preserving this structure:

   public/index.html
   vercel.json
   README.md

3. In Vercel, choose Add New → Project and import that repository.
4. Keep Root Directory at the repository root (not public). If asked, select the Other framework preset. No build command is needed. The bundled vercel.json selects public as the output directory and sets basic response headers.
5. Deploy and open the HTTPS website address Vercel supplies.
6. Run the launch tests below before sharing. Change the project name or connect your own domain in the project's settings when ready.

Git-based updates: edit public/index.html in the repository and commit the change to trigger another deployment when Git integration is enabled.

## Alternative: Vercel CLI
With Node.js installed, open a terminal in the extracted folder containing vercel.json and run:

```bash
npx vercel --prod
```

Sign in and follow the prompts to create/link your Vercel project. Keep the project root as this folder. The supplied configuration sets public as the static output. The command installs/runs Vercel's CLI on your computer; it does not install an app framework. Check Vercel's current pricing, terms, and interface labels. Account creation, any charges, custom domain purchases, and DNS changes are your responsibility.

## Package structure
- public/index.html: the entire app, including embedded listing data and styles.
- vercel.json: static-output and security-header configuration.
- README.md: this launch and maintenance guide; it is outside the public website folder.
- venue-registry.csv: 70 app listings plus 15 held/excluded candidates with source links, dates and caveats. Not loaded by the app; edit the JSON embedded in public/index.html to change the site.

This package is prepared for deployment but has not been deployed to your account.

## Test before sharing
- Enter St. Louis, Central West End, Clayton, and ZIP 63108; select filters.
- Search another US city; choose a geocoding result and confirm that uncovered areas show an honest empty state.
- Allow and deny browser location permission. Geolocation requires HTTPS or localhost; embedded previews may block it.
- Verify source links, directions, and map loading on iPhone/Android and desktop.
- Save a venue, reload, and use Saved only. Saved places are browser-local, not account-backed.
- Use Happening now. All pilot venues use America/Chicago time, including daylight-saving changes. End times are exclusive. It indicates a schedule match, not that the venue is live-confirmed open.
- Confirm hours and offers directly with each venue, especially Bowood, before promoting the directory.

## Edit or add listings
Open public/index.html in a text editor. Find the script tagged id="venue-data". Its JSON array is the listing database. Each item has name, address, neighborhood, approximate coordinates, types, offers, schedule, sessions, source links, checked, quality, and uncertain. Legacy days/start/end fields are retained, but the sessions array governs live-time matching.
- sessions: array of {days, start, end, confirmed}; days use Sunday=0 through Saturday=6. Multiple sessions support weekday/weekend variations. Never fabricate an end for until-close windows.
- Each session start/end: minutes after midnight in America/Chicago (4 PM=960; 5:30 PM=1050).
- schedule is the human-readable label; keep it consistent with session windows. Sessions with null times or confirmed=false never match Happening now.
- types must use food, beer, wine, or cocktails.
- coordinates in the pilot are approximate manually placed points, not surveyed locations.
- Source each offer and schedule to official venue/operator pages. Keep source URLs and checked dates current; change the human-readable checked-date footer and coverage text as needed.
- uncertain=true removes the listing from Happening now. quality="guide" excludes it too. Set quality="official" and mark individual session confirmed=true only after checking direct official sources. Unknown recurring days use days=[], so they do not appear in a day-specific filter.
- Source attribution attributes support the preview; external users see normal Menu source links.
- New cities require verified entries. The app does not discover or scrape bars automatically.
- This version supports multiple same-day happy-hour windows per listing using a sessions array. Overnight matching, exception dates, and non-Central time zones require further changes. Open-ended until-close sessions have end=null and confirmed=false; no closing time is invented. Do not add those without changing the code.

Commit and redeploy after edits (or rerun the Vercel CLI). For ongoing public use, move data to a CMS or database rather than repeatedly editing HTML.

## External services and privacy
- Common St. Louis location names and listed ZIP codes resolve locally to approximate centers.
- Other explicit US location searches use public OpenStreetMap Nominatim over HTTPS. Searches are limited to roughly one request per second per browser and are not made on each keystroke. Public services have rate limits, availability limits, CORS restrictions, and usage policies. Multiple users can exceed aggregate limits. Review Nominatim's current usage policy before public marketing; use a contracted geocoding provider and server-side global rate limiting/cache for growth. The endpoint is in the search-form handler and can be replaced.
- Precise browser coordinates are kept in memory and are not sent to the geocoder. Opening an embedded map sends the venue's coordinates to OpenStreetMap. External menus and Google Maps directions use third-party sites. Hosting/external providers may log standard requests.
- Saved venue IDs are kept in localStorage when available. There are no analytics scripts, login, payments, submission database, tracking pixels, or background monitoring.
- No secrets should be placed in this HTML. Any future private API key belongs server-side.
- No public correction-submission endpoint is included. The footer is explicit about this; add a monitored contact or moderated submission service before marketing.

## Pilot boundaries
JavaScript syntax and automated logic checks cover filters, local ZIP lookup, geocoding error handling, mocked location permission outcomes, saved places, uncovered locations, schedule boundaries, and daylight-saving time. Full visual/browser testing was not available during this build. Real hosted geolocation permissions, external geocoding availability, map access, responsive layout, and hosting deployment still need your launch test. Straight-line distances are estimates. Holiday closures, expired offers, and live venue status are not detected. This app is not yet publicly hosted.

## Expansion notes — October 1, 2026
- Geography: St. Louis City and nearby inner-ring suburbs, plus Kirkwood/Des Peres. St. Charles, Illinois Metro East and outer west/north county are not covered in this pass.
- Forty-seven official-page listings include several partial/conflicting schedules. They are source-checked, not restaurant-confirmed; source access date does not imply the page was recently updated.
- Twenty-three guide-only leads are hidden by default. Visitors may opt in with Include unconfirmed guide leads. They never appear as Happening now. Older reported offers remain explicitly labeled.
- Fifteen held/excluded checks are in the registry, including closure notices and unreadable/conflicting schedules. They are not public app results.
- The 70 entries are unique physical venues; multiple sessions do not inflate the count. Co-located Olive + Oak and Perennial are separate concepts, but both are unconfirmed guide leads in this build.
- Search Whole directory with the radius selector to inspect everything. No venue is labeled open based on live data.
- Existing hosting/geocoding/browser launch checks still apply. Automated code and data checks do not replace phone/browser testing.

## Weekly source-check workflow
This package now includes a read-only GitHub Actions source checker. See WEEKLY-CHECKS.md for activation, the initial baseline, approval commands, safety limits and reports. It never edits offers or verification dates automatically. No Vercel configuration change is required. Commit .github and .source-check as well as scripts/tests; these dot folders can be hidden on your computer.

## Des Peres expansion — October 2, 2026
Twenty new entries added; Billy G’s and Wasabi Clayton updated without duplication. The Des Peres shortcut and automatic seven-mile radius have been removed. Manual location search and the normal radius choices remain available. Center: Des Peres City Hall, 12325 Manchester Road, manually estimated at 38.6007, -90.4480. Straight-line radius, not driving distance. Registry columns flag near-boundary estimates. Scope is a research pass, not every venue in the area; no closed/unconfirmed business is promoted from search text alone. Existing city coverage is retained.
