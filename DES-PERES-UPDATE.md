# Des Peres expansion — Calvin Loves Happy Hour

- 20 new directory entries added; full app now has 70 entries. Existing city listings were retained.
- The approximate seven-mile Des Peres view includes 51 entries: 32 official-page listings (some incomplete/conflicting) and 19 guide-only leads hidden by default. These are estimates, not a verified radius census.
- Center is Des Peres City Hall, 12325 Manchester Road. Point is manually estimated, not precision-geocoded; distance is straight line. Near-boundary entries are flagged.
- The Des Peres shortcut and forced seven-mile radius have been removed. Visitors may still type Des Peres and select any available radius.
- The weekly checker is included and discovers the updated source list automatically; it is not enabled until uploaded to GitHub and activated as described in WEEKLY-CHECKS.md.
- Source check dates now display per listing rather than one hardcoded date.
- New food/drink sessions are modeled separately where published (e.g., Helen Fitzgerald’s).
- This pass does not guarantee every local venue. Blank/image menus, incomplete schedules and disputed prices remain flagged; guide-only leads stay opt-in.
- Trainwreck Rock Hill is held out because its official homepage currently says closed for remodeling. Dewey’s Kirkwood and the two Napoli locations are held rather than assumed to offer happy hour.

## Before uploading
Use this package in place of the prior package. It contains the app, Vercel configuration, registry, weekly workflow, checker scripts, and setup guides together. If you have made independent app edits, merge this updated public/index.html carefully. Keep existing accepted .source-check/approved-snapshots.json if a baseline already exists; do not overwrite it with the empty starter. No Vercel configuration change is needed.

Validation covers code/data and offline logic, not live source checks or full visual/device testing. Test the deployed site before sharing publicly.
