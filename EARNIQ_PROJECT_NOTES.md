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
- ✅ Login + cloud sync version PUSHED to GitHub and LIVE on myearniq.com (commit a156f8f, Netlify deployed it automatically)
- ✅ Laptop, GitHub, and Mac mini all have the same latest code
- ✅ Supabase Site URL set to https://myearniq.com (Authentication → URL Configuration)
- ✅ Adam's admin account created and email confirmed (adamrussell8@yahoo.com, role: admin)
- 🔶 NEXT UP: log a test deal at myearniq.com and confirm it appears in Supabase (Claude can verify from its side). Zero deals on the server as of bedtime July 9.

## Gotcha we hit twice tonight
Stale `.git/index.lock` file blocks git on BOTH computers. Fix:
`rm -f ~/Desktop/earniq-web/.git/index.lock` then re-run the git commands.

## Known follow-ups (not started)
- Real email service for signup confirmation emails (Supabase's built-in sender is limited to a few per hour — fine for testing only)
- Update earniq.app privacy wording before promoting the login version ("stays on your phone" → needs revising since data now syncs to user accounts)
- Landing page "Use EarnIQ on the Web" button — added in Replit, but Cloudflare still serves the OLD version. Needs: fresh build downloaded from Replit → re-uploaded to Cloudflare.
- Consider moving landing page deploys to GitHub → Cloudflare so updates are one push
- From older handoff: Zoho SPF record in Cloudflare, swap App Store link on earniq.app when Apple approves, verify earniq.app/terms loads (looked possibly broken on July 9), marketing push per EarnIQ-Launch-Plan.md

## To start the next session
Open Claude (Cowork), connect the Desktop folder, and say:
"Read EARNIQ_PROJECT_NOTES.md in my earniq-web folder and pick up where we left off."
