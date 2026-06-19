# Budget — deploy + sync

## Files in this bundle
- `index.html` — the app
- `manifest.webmanifest` — PWA manifest
- `sw.js` — service worker (offline + installable)
- `icon-192.png`, `icon-512.png`, `icon-512-maskable.png`, `icon-180.png` — app icons

## 1. Deploy to your domain
Drop all of these into your `budget` repo/folder so they sit together at `bernsjnr.github.io/budget/`. They must be in the same directory — the manifest, service worker and icons are referenced by relative path.

Commit and push. Give Pages a minute.

## 2. Install on each phone
Open `bernsjnr.github.io/budget/` on the phone.
- **iPhone (you + Lola):** Share button → Add to Home Screen.
- **Android:** an install button appears in Setup → Install, or the browser offers it.

Installed, it runs full screen and works offline.

## 3. Turn on sync (both phones share one budget)
Without this, each phone keeps its own copy. To share the same numbers:

1. Create a free project at supabase.com.
2. In the SQL editor, run:
   ```sql
   create table budgets (
     id text primary key,
     data jsonb,
     updated_at timestamptz default now()
   );
   alter table budgets enable row level security;
   create policy "anon access" on budgets for all using (true) with check (true);
   ```
3. In Supabase → Project Settings → API, copy the **Project URL** and the **anon public key**.
4. In the app → Setup → Sync across phones:
   - paste the URL and anon key
   - tap **Generate** for a Household ID
   - **Save & connect**
5. On the second phone, enter the **same three values** (same Household ID is the important one). Both now read and write the same budget.

### A note on security
This setup trusts anyone who has the anon key + your Household ID. The anon key is public by design; the Household ID is your real secret, so keep it private and use the generated random one. For stronger protection I can add Supabase Auth (email login, per-user access) in a later pass.

## Screenshot reading on your domain
The Monzo screenshot reader uses Claude's vision. It works when the app runs inside Claude. On your own domain it needs a small serverless proxy holding an API key (a Supabase Edge Function does it). Until then, on-domain the screenshot button falls back to manual entry, and everything else works. Say the word and I'll wire the function.
