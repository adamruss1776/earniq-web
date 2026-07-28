# EarnIQ — Project Notes (updated July 21, 2026)

## What EarnIQ is
Commission tracker for car salespeople. Built by Adam Russell.
- **iPhone app** — submitted to the App Store, under review. Built in Replit ("EarnIQ Sales").
- **Web app** — live at **myearniq.com** (the "command center" dashboard).
- **Landing page** — live at **earniq.app** (marketing site).

## Where everything lives
| Piece | Where | Notes |
|---|---|---|
| Web app code | GitHub repo `adamruss1776/earniq-web` | One file: index.html |
| Web app hosting | Netlify, project "myearniq" | Auto-publishes when you push to GitHub |
| Web app domain | Namecheap (myearniq.com) → points to Netlify | Connected and working |
| Landing page code | Replit, project "EarnIQ Landing page" | Vite app, build output in artifacts/earn-iq-landing/dist/public |
| Landing page hosting | Cloudflare, Worker named "earniq" | Files were uploaded by hand on July 7 |
| User accounts + data | Supabase, project "earniq" (org: Pinnacle AI) | Tables: profiles, deals, spiffs, settings — all secured with RLS |

## Current status (end of July 9 session, Mac mini)
- ✅ Login + cloud sync LIVE on myearniq.com. Cloud sync verified end to end.
- ✅ Adam's admin account working (adamrussell8@yahoo.com, role: admin). Test account: adam@pinnaclecrm.ai (regular user).
- ✅ **PAYWALL LIVE AND FULLY TESTED with real money.** First 15 deals free (lifetime total, no monthly reset), then EarnIQ Pro: $3.99/mo or $29.99/yr via Stripe. Verified: pay → Pro unlocks in seconds; cancel → paywall comes back. Test charge refunded.
- ✅ Stripe is in LIVE mode (real cards work). Webhook → Supabase edge function "stripe-webhook"; checkout via "create-checkout". Secrets stored in Supabase Edge Functions settings.
- ✅ Admin accounts bypass the paywall by design.
- 🔶 Landing page (Desktop/earniq-site): "Use EarnIQ on the Web" button + honest privacy wording DONE locally but NOT uploaded to Cloudflare yet.

## July 15: Apple rejection + resubmission
- Build 34 REJECTED: (1) 3.1.2c missing Terms of Use link in metadata, (2) 2.1b paywall loaded indefinitely on purchase.
- Root cause of #2: race condition in lib/revenuecat.tsx — queries fired before RevenueCat configure(), cached null, never refetched. FIXED (queries now gated on init, retry + "Retry" button in PaywallModal instead of infinite spinner). Also: subscription review screenshots were missing in App Store Connect (now uploaded, resized to 1290x2796 — iPhone screenshots come out 1320-wide which Apple rejects).
- IMPORTANT: the Mac mini IS the iOS build machine — EarnIQ-Sales folder here has the current code even though git log looks old (builds aren't always committed). Replit copy is stale (stopped at build 31).
- ✅ Build 36 submitted July 15 with all fixes. Terms link added to description, subs attached to version, display names now "EarnIQ Pro Monthly/Annual".
- Paywall confirmed WORKING in TestFlight build 36.
- Rejection #2 (July 15): 3.1.2c again but narrower — needed Terms + Privacy links INSIDE the app on the paywall. Added both links to PaywallModal (open earniq.app/terms and /privacy), built + RESUBMITTED (build 37) with screen-recording proof attached in Resolution Center. 2.1b purchase bug did NOT recur — fix held. 🤞

## July 10 session (Mac mini) — web app features
- ✅ Deal editing: pencil (✎) on every deal row opens the deal for changes.
- ✅ Goals page charts: "The Year So Far" (cumulative earnings vs goal pace) + "Months Won" (green bar = beat monthly goal).
- ✅ Web scanner now uses the AI OCR server (earn-iq-sales.replit.app/api/ocr) — same reader as the iPhone app, in-browser Tesseract kept as fallback. Vehicle recognition confirmed working.
- ✅ Stock number extraction added to the OCR server by Replit agent (web side pre-wired to receive it). NEEDS: Replit redeploy + verification scan.
- ✅ WASHOUT BINDER Phase 1: new "Washouts" section — upload manager's month-end sheet (photo/PDF), filed by month/year, private per-user storage (Supabase bucket "washouts" + washouts table, RLS secured). View/remove sheets.
- 🔶 WASHOUT PHASE 2 (not started, the killer feature): AI reads the manager's sheet and cross-references against logged deals → discrepancy report (missing deals, gross mismatches, dollar delta). DECIDED: binder storage is free, AI Compare is Pro. Needs: washout-extraction endpoint on the Replit api-server + compare UI.
- NOTE: Adam sells luxury/exotics — the Lamborghini/Rolls-Royce deals in his account are REAL deals, do not treat as test data.
- Adam's real usage begins now: admin account = adamrussell8@yahoo.com (unlimited). Test account: adam@pinnaclecrm.ai.
- ✅ Landing page (with official App Store badge + web button + honest copy) uploaded to Cloudflare and confirmed live.
- ✅ Privacy policy + Terms rewritten July 10 to cover the web app (accounts, cloud storage, Stripe). 🔶 NEEDS: re-upload earniq-site to Cloudflare + "Purge Everything" in Caching so old cached pages clear worldwide.
- Adam is now dogfooding: entering his real deals daily in the admin account to verify tracking. Expect bug reports.

## Gotcha we hit twice tonight
Stale `.git/index.lock` file blocks git on BOTH computers. Fix:
`rm -f ~/Desktop/earniq-web/.git/index.lock` then re-run the git commands.

## Known follow-ups (not started)
- Stripe → Settings → Business → Public details: change public name + statement descriptor from "Pinnaclecrm.ai" to "EarnIQ" BEFORE real customers pay (it's what shows on their card statement)
- Business registration: operating as "EarnIQ" in Florida needs a fictitious name (~$50 at sunbiz.org + newspaper notice) or an LLC — talk to an accountant/attorney before real revenue ramps up
- A way for subscribers to cancel from inside the app (Stripe customer portal) — right now they'd have to email support
- Automatic backups of user data (deletes are permanent, no undo) — important before users build up months of history
- OCR costs: every web scan hits OpenAI via the Replit server; fine at current scale, watch usage as users grow
- Real email service for signup confirmation emails (Supabase's built-in sender is limited to a few per hour — fine for testing only)
- Update earniq.app privacy wording before promoting the login version ("stays on your phone" → needs revising since data now syncs to user accounts)
- Landing page "Use EarnIQ on the Web" button — added in Replit, but Cloudflare still serves the OLD version. Needs: fresh build downloaded from Replit → re-uploaded to Cloudflare.
- Consider moving landing page deploys to GitHub → Cloudflare so updates are one push
- From older handoff: Zoho SPF record in Cloudflare, swap App Store link on earniq.app when Apple approves, verify earniq.app/terms loads (looked possibly broken on July 9), marketing push per EarnIQ-Launch-Plan.md

## July 21: iPhone app login system + app name fix

### App name fix
- "(d62481)" suffix in the App Store name comes from Replit's auto-naming. To change it, Apple requires a new version.
- Bumped app.json: version 1.0.0 → 1.0.1, buildNumber 37 → 38.
- Adam needs to: go to App Store Connect → App Information → change Name from "EarnIQ (d62481)" to "EarnIQ" → create a new version (1.0.1) → submit.

### iPhone app login/account system (code complete, needs build + test)
Files changed:
- **NEW: lib/supabase.ts** — Supabase client for React Native, same project as web app (wwzliiwfikcfktowxvol). Uses JWT anon key + AsyncStorage for session persistence.
- **NEW: context/AuthContext.tsx** — provides user/session/isGuest to the app. Listens for Supabase auth state changes.
- **components/LoginScreen.tsx** — complete rewrite: real Sign In + Sign Up + Forgot Password via Supabase auth. Still has "Continue without account" guest mode. Fixed footer links from earniq.replit.app → earniq.app. Added Terms link alongside Privacy link.
- **context/DataContext.tsx** — dual-mode: when logged in, reads/writes deals+spiffs+settings to Supabase (same tables as web app); when guest, uses AsyncStorage (local). Washouts stay local-only (web and mobile use different washout schemas). Maps between iPhone field names (vehicleName, frontGross, etc.) and DB column names (vehicle, front, etc.).
- **app/_layout.tsx** — tracks isGuest vs logged-in state. Wraps everything in AuthProvider. LoginScreen.onComplete now passes userId for account logins.
- **lib/logout.ts** — now also calls supabase.auth.signOut() and clears GUEST_MODE_KEY.
- **app/settings.tsx** — shows user email when logged in, shows "Guest (no account)" otherwise. Fixed legal URLs from earniq.replit.app → earniq.app. Support email: adam@earniq.app.
- **package.json** — added @supabase/supabase-js and react-native-url-polyfill.

### Privacy + Terms updated
- earniq-site/privacy.html — rewrote to reflect that BOTH the iPhone app and web app now offer optional accounts. Guest mode (local-only) still available. Updated date to July 21.
- earniq-site/terms.html — updated Accounts section to cover iPhone app accounts. Updated date to July 21.
- NEEDS: re-upload earniq-site folder to Cloudflare + Purge Everything.

### Database trigger confirmed
- `handle_new_user` trigger on auth.users auto-creates profiles + settings rows on signup. Adam's emails get role='admin'.

### What's next
1. Adam: run `npm install` (or `pnpm install`) in EarnIQ-Sales/artifacts/earniq to get new packages
2. Adam: `eas build --platform ios --profile production` then `eas submit`
3. Adam: in App Store Connect, create version 1.0.1, change Name to "EarnIQ", submit
4. Adam: re-upload earniq-site to Cloudflare + Purge Everything for updated legal pages
5. Test: sign up on iPhone app, verify deals sync between phone and myearniq.com

## July 26–27: Founder codes + onboarding questionnaire

### ⚠️ READ THIS FIRST NEXT SESSION
**Always run `git fetch origin && git log HEAD..origin/main` before writing code.**
On July 27 Claude built a full founder-code redemption system that already existed —
Adam had built and pushed one on July 26 at 15:40. Two hours of duplicate work, caught
only because the push was rejected. The Mac mini is not always the newest copy.

### Founder codes — HOW IT ACTUALLY WORKS
- 15 codes: FOUNDER-001 … FOUNDER-015, in Supabase table `founder_codes`.
- Redemption is a **Postgres RPC**: `redeem_founder_code(p_code text)`, SECURITY DEFINER.
  Called from the browser via `sb.rpc(...)`. There is NO edge function for this.
- Grants `subscriptions.status = 'founder'` (no expiry). `isProSub()` treats 'founder' as Pro.
- Entry points (4 total, added July 27):
  1. **Sidebar "Founder Code" link** — always visible to non-Pro. THE reliable one.
  2. 🎟 button in the amber banner — only at 10+ deals.
  3. "Have a founder code?" in the upgrade modal — only at 15 deals.
  4. Optional field on the signup form — stored in localStorage as
     `earniq_pending_founder_code`, redeemed on first session (survives email confirmation).
- **Why a tester couldn't find it:** originally only #2 and #3 existed, both gated behind
  deal count. Testers with 3 deals saw nothing.

### Security fix (July 27)
`founder_codes` had an RLS policy `"Anyone can check codes"` with `USING (true)` — any
signed-in user could read all 15 valid codes. Replaced with a policy limiting SELECT to
your own redeemed row. All 15 codes verified unused at the time of the fix.

### Onboarding questionnaire (web only)
- 4 questions → a personalized "here's where you stand" summary → drops into pay plan setup.
- Answers saved to new table `public.onboarding` (RLS: own rows only).
- Only shows when: no onboarding row AND zero deals AND no `earniq_onboarded_<uid>` local flag.
  Existing users are never interrupted.
- Copy lives in `ONBOARD_Q`; the summary logic is `buildMirror()`.

### Loose ends
- 🔶 Unused edge function `redeem-founder-code` (Claude's duplicate) still deployed.
  Delete from Supabase dashboard → Edge Functions. Nothing calls it.
- 🔶 Neither founder codes nor onboarding exist on iOS. Web-first by choice — let the
  15 testers validate before spending an App Store review cycle.
- 🔶 `redeem_founder_code` has no race guard on the UPDATE (no `AND redeemed_by IS NULL`).
  Harmless at 15 codes; worth hardening if codes ever go out at scale.

## July 27–28: AI Sales Coach + industry metrics

### ⚠️ THE FETCH RULE — READ BEFORE WRITING ANY CODE
`git fetch origin && git log HEAD..origin/main` **on every repo, every session.**
This was violated twice in two days:
- Jul 27: rebuilt a founder-code system Adam had already shipped (2 hrs wasted)
- Jul 28: edited Pinnacle's index.html while 9 commits / 11 days behind

Root cause found Jul 28 on pinnacle-crm: the repo had **no fetch refspec**, so
`origin/main` never existed locally, `git status` could never report "behind", and the
Mac drifted silently. Fixed with:
`git config remote.origin.fetch "+refs/heads/*:refs/remotes/origin/*"`
**Check the other repos for the same fault.** Tell: `git status` never says "behind".

### AI COACH — live on myearniq.com
Three pillars, all Pro-gated (admins and `founder` status included).

**Backend — two Supabase edge functions, project `earniq`:**
- `coach` — deal reviews + weekly report cards. Cheap model.
- `coach-ask` — the guided Q&A. Strong model for cached answers, cheap for personalized.
- Key lives in Supabase → Edge Functions → Secrets. Works with either
  `ANTHROPIC_API_KEY` or `OPENAI_API_KEY`; the code detects which is present.
  **Currently running on OpenAI.**

**Cost design (this is the important part):**
- The 12 generic coaching answers are IDENTICAL for every user → generated once,
  cached in `coach_answers`, served free forever after. 15 users asking the same
  question = 1 API call total.
- Deal reviews cached per deal in `coach_insights` (unique index prevents double-billing).
- Weekly reports cached per user per week (unique index).
- Free-text questions rate limited to 10/user/day via `bump_coach_usage(p_limit)`.
- Personalized "My Numbers" answers are per-user, cheap model, not cached.

**Tables added:** `coach_insights`, `coach_answers`, `coach_usage`, `onboarding`.

**Voice — do not soften this.** First attempt produced corporate filler
("Acknowledge Their Concern", "Navigating Customer Hesitation"). A 10-year industry
veteran called the rewrite "pretty damn good." What fixed it:
1. A full worked example in the prompt, not just instructions
2. Explicitly banned phrases (acknowledge/validate/rapport/"it's a big decision")
3. The test that catches filler: *would this advice work equally well selling insurance?*
4. Stronger model for the cached-forever answers (~$0.25 one time, total)
- Admin-only `regenerate: true` flag refreshes a single weak answer.
- **Hard rule in every prompt: use only the user's own numbers. Never invent an
  industry benchmark.** A salesperson may repeat these figures to their manager.

### Industry metrics on the dashboard (the ReverseRisk lesson)
A tester compared EarnIQ to **ReverseRisk (Reynolds & Reynolds)** and found it "thin."
Assessment: not a competitor — ReverseRisk is a DMS-integrated store P&L tool for GMs
and controllers; a salesperson can't buy it and it doesn't show personal commission.
It is literally the asymmetry EarnIQ exists to fix. But three ideas were worth taking:
- **PVR** (per vehicle retailed) — front/back/total, split new vs pre-owned
- **Actual / On Pace / vs Last Month** columns with "Day X of Y"
- Variance arrows
Built into `buildStats()` + new `PerformanceTable` component. Math unit-tested.
- Comparison is **same-day-to-same-day** (day 27 vs day 27), never partial-vs-full month.
- Shows dashes + an explanation when there's no prior month. Never fabricates a baseline.

### Founder codes (resolved Jul 27)
- Adam's `redeem_founder_code(p_code)` RPC is the real one. Claude's duplicate edge
  function was deleted.
- Four entry points now: sidebar link (always visible to non-Pro), 🎟 banner button
  (10+ deals), upgrade modal link (15 deals), signup field (held in localStorage as
  `earniq_pending_founder_code`, redeemed on first session).
- RLS hole closed: `founder_codes` had `USING (true)` — any signed-in user could read
  all 15 valid codes.
- **All 15 codes still unredeemed as of Jul 28.**

### Onboarding questionnaire
4 questions → personalized "here's where you stand" summary → pay plan setup.
Only fires for accounts with no onboarding row AND zero deals. Copy in `ONBOARD_Q`,
summary logic in `buildMirror()`.

### Real users so far
- tyler_grant@hotmail.com — signed up Jul 27, returned once, **0 deals**
- jessicalynnjames@hotmail.com — signed up Jul 26, returned once, **0 deals**
Two people, two sessions each, zero deals logged. **This is the most important
unanswered question in the project** — something blocks the gap between "I have an
account" and "I logged a deal." Ask them directly before building anything else.

### Open
- 🔶 11 of 12 generic coach answers not yet pre-warmed — a tester could be first to
  generate one and see it before Adam does
- 🔶 `earniq-site` not uploaded to Cloudflare (AI Coach section, "Built for the sales
  floor", App Store button on "Get the App", support@ on all 3 pages) + Purge Everything
- 🔶 EarnIQ web PVR/pace table may not be pushed yet
- 🔶 Coach + onboarding + PVR are **web only**. iOS unchanged and still in App Store review.
- 🔶 Landing page sample of the coach's answer was written from memory — match it to the
  real cached `ob1` output

## To start the next session
Open Claude (Cowork), connect the Desktop folder, and say:
"Read EARNIQ_PROJECT_NOTES.md in my earniq-web folder and pick up where we left off."
