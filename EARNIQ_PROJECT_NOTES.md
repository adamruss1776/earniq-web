# EarnIQ — Project Notes (updated July 9, 2026, late evening)

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

## Gotcha we hit twice tonight
Stale `.git/index.lock` file blocks git on BOTH computers. Fix:
`rm -f ~/Desktop/earniq-web/.git/index.lock` then re-run the git commands.

## Known follow-ups (not started)
- Stripe → Settings → Business → Public details: change public name + statement descriptor from "Pinnaclecrm.ai" to "EarnIQ" BEFORE real customers pay (it's what shows on their card statement)
- Business registration: operating as "EarnIQ" in Florida needs a fictitious name (~$50 at sunbiz.org + newspaper notice) or an LLC — talk to an accountant/attorney before real revenue ramps up
- A way for subscribers to cancel from inside the app (Stripe customer portal) — right now they'd have to email support
- Real email service for signup confirmation emails (Supabase's built-in sender is limited to a few per hour — fine for testing only)
- Update earniq.app privacy wording before promoting the login version ("stays on your phone" → needs revising since data now syncs to user accounts)
- Landing page "Use EarnIQ on the Web" button — added in Replit, but Cloudflare still serves the OLD version. Needs: fresh build downloaded from Replit → re-uploaded to Cloudflare.
- Consider moving landing page deploys to GitHub → Cloudflare so updates are one push
- From older handoff: Zoho SPF record in Cloudflare, swap App Store link on earniq.app when Apple approves, verify earniq.app/terms loads (looked possibly broken on July 9), marketing push per EarnIQ-Launch-Plan.md

## To start the next session
Open Claude (Cowork), connect the Desktop folder, and say:
"Read EARNIQ_PROJECT_NOTES.md in my earniq-web folder and pick up where we left off."
