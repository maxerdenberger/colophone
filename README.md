# colophon — weekend ship guide

Five HTML files. No backend. Ships from Framer or any static host.

## the files

| File | What it is | Public URL |
|---|---|---|
| `index.html` | Landing page | `colophon.xyz/` |
| `pricing.html` | Three pass tiers, links to Stripe | `colophon.xyz/pricing` |
| `success.html` | Post-checkout, shows access link | `colophon.xyz/success` |
| `bench.html` | The product. Embeds your 662-creative dataset. | `colophon.xyz/bench` |
| `refresh.html` | Creative refresh form (the growth engine) | `colophon.xyz/refresh` |
| `apply.html` | New creative application form | `colophon.xyz/apply` |

## the flow

```
visitor → index → pricing → stripe → success → bench (with ?key=...)
                                          ↓
                                    bookmarks link
                                          ↓
                                  comes back, browses
                                          ↓
                                  email creative directly
                                          ↓
                              you log the contact event
```

For your existing 663 creatives:
```
mail merge from sheet → refresh.html?name=…&email=… → submits
                                          ↓
                              their card stays live
                                  3 referrals collected
                                  1 buyer (optional, earns featured perk)
                                          ↓
                                  you email the referrals
                                  bench grows
```

## stripe setup (the only real config)

1. **Create three Payment Links** in Stripe dashboard:
   - Day pass — $29 USD, one-time
   - Week pass — $49 USD, one-time
   - Monthly — $129 USD, recurring
2. **Set "After payment" → Custom URL** for each, pointing to:
   `https://colophon.xyz/success?session_id={CHECKOUT_SESSION_ID}`
3. **Edit pricing.html**, find these three placeholders and paste your real Stripe URLs:
   - `REPLACE_DAY_PASS_LINK`
   - `REPLACE_WEEK_PASS_LINK`
   - `REPLACE_MONTHLY_LINK`
4. **Stripe sends an automatic receipt email** with the success page URL — that becomes the access link.

## token validation (optional, can skip for v1)

The bench page accepts ANY `?key=…` param right now. Anyone with a link gets in.

For v1 this is fine — Stripe collected payment, the link is in their inbox, share-as-feature is good viral mechanics. If you want real validation later:

1. Stripe webhook → Zapier/Make → Google Sheet "Tokens" tab
2. Each row: `token | email | tier | created_at | expires_at`
3. Bench page fetches the published Sheet CSV on load, checks the token from the URL against it
4. Expired or unknown → show the gate

I left the gate logic in `bench.html` ready to wire — find the `// Production: validate token...` comment.

## moving to live data (when you're ready)

The bench currently has all 662 records embedded inline. To make it live:

1. Move your master sheet to Google Sheets, publish to web as CSV
2. In `bench.html`, replace `window.__BENCH_DATA__` initialization with a `fetch()` to your published CSV URL
3. Parse with PapaParse (already on most CDNs)
4. The bench updates whenever you update the sheet

Until then, when you want to update the bench, regenerate the JSON from your CSV and paste it back into bench.html.

## the refresh email

To kick off the growth engine, send all 663 a personalized link:
```
https://colophon.xyz/refresh?name=Jane%20Doe&email=jane@example.com&rate=200&fee=5000&portfolio=https://janedoe.com
```

Use the formula approach we worked out earlier in your master sheet, then mail-merge through Gmail.

## tracking contacts (already wired)

When an agency clicks "contact" on a card, the bench logs it to `console.log` (demo). Replace the `trackContact()` function in bench.html with a real fetch to:
- A Make/Zapier webhook → Google Sheet
- Or a simple endpoint you spin up later

This gives you a live feed of what the bench is doing for your customers.

## what's NOT built (yet)

- **Real token validation** — see above
- **Live Sheets feed** — see above

These can wait. What's here is enough to ship and start collecting money this weekend.

## the voice

I kept the Apostrophe-ish voice throughout: lowercase, strikethroughs for the things you're NOT, dry self-aware lines like "we're a door, not a middleman" and "it's not a ranking. it's a room." If you want anything dialed up or down, edit copy directly — most of it is in obvious places.
