# BTReDS SQUAD FLASH PEAK — Google OAuth + Cloudflare Workers

Cloudflare Worker: `btreds-squad-flashpeak`

## Structure
- `public/index.html` — website + Supabase + Continue with Google
- `wrangler.jsonc` — Cloudflare Workers Static Assets configuration
- `scripts/setup_patch.sql` — Supabase trigger patch for Google profile names

## Supabase
Google callback:
`https://oztmylktxjymmpkzroim.supabase.co/auth/v1/callback`

Allowed app URL:
`https://btreds-squad-flashpeak.leozeo1108.workers.dev/`

## Security
- No Google Client Secret is stored in this project.
- The Supabase publishable key is intended for browser use with RLS enabled.
- Never add a Supabase service_role key or Google Client Secret to `public/`.
