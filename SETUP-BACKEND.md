# Golf Hide — Backend & Auto-Deploy Setup

Two things to set up so you can manage Match Play / OOM data without re-uploading the ZIP:

1. **Supabase** — the database that holds Match Play results, the TOC text, and OOM standings. Free.
2. **GitHub → Netlify auto-deploy** — so future code changes go live automatically when I push them. Free.

You only do this once. After that, you edit data from your phone and I push code from my end. Total time: ~15 minutes.

---

## Part 1 — Supabase (5 minutes)

### Step 1.1 — Create the project

1. Go to https://supabase.com and click **Start your project** (top right).
2. Sign up with GitHub or with your email. No credit card needed for the free tier.
3. After signup, click **New project**.
4. Fill in:
   - **Name**: `laguna-lads` (or whatever you like)
   - **Database password**: click "Generate a password" and **save it somewhere** (1Password, Notes app, anywhere safe). You won't need it day-to-day, but you'll need it if you ever recover the project.
   - **Region**: **Southeast Asia (Singapore)** — closest to you for lowest latency.
   - **Pricing plan**: Free.
5. Click **Create new project**. Wait ~2 minutes while it provisions.

### Step 1.2 — Send me two values

Once the project is up:

1. Left sidebar → **Project Settings** (gear icon) → **API**.
2. Copy two things and paste them back to me in this chat:
   - **Project URL** (it's a single line like `https://abcxyz123.supabase.co`)
   - **anon public** key (long string starting with `eyJhbGc...` — make sure it's the **anon public** one, NOT the **service_role** one. The service_role is secret; the anon is meant to be public.)

I'll wire those into the app code on my side.

### Step 1.3 — Run the SQL to create tables

Still in your Supabase project:

1. Left sidebar → **SQL Editor** → click **New query**.
2. Paste the entire block below and click **Run** (or press Cmd/Ctrl + Enter):

```sql
-- Match Play data lives in a single row per cup
create table if not exists matchplay_cups (
  id text primary key,
  data jsonb not null,
  updated_at timestamptz default now()
);

-- OOM data — one row, holds the points table + custom adjustments
create table if not exists oom_data (
  id text primary key,
  data jsonb not null,
  updated_at timestamptz default now()
);

-- Anyone can read; only authenticated users can write
alter table matchplay_cups enable row level security;
alter table oom_data        enable row level security;

create policy "public read mp" on matchplay_cups for select using (true);
create policy "auth write mp"  on matchplay_cups for all
  using (auth.role() = 'authenticated')
  with check (auth.role() = 'authenticated');

create policy "public read oom" on oom_data for select using (true);
create policy "auth write oom"  on oom_data for all
  using (auth.role() = 'authenticated')
  with check (auth.role() = 'authenticated');

-- Seed the cup with the current data
insert into matchplay_cups (id, data) values ('h1-2026', '{"placeholder": true}')
  on conflict (id) do nothing;

insert into oom_data (id, data) values ('current', '{"placeholder": true}')
  on conflict (id) do nothing;
```

You should see "Success. No rows returned" at the bottom. Done.

### Step 1.4 — Create your admin user

1. Left sidebar → **Authentication** → **Users** → **Add user** → **Create new user**.
2. Email: your email address. Password: pick a strong one (this is what you'll use to log in on your phone).
3. Tick **Auto Confirm User** so you don't need to click an email link.
4. Click **Create user**.

That's the Supabase side done. Send me the URL + anon key from Step 1.2 whenever you're ready and I'll do the rest of the wiring.

---

## Part 2 — GitHub → Netlify auto-deploy (10 minutes)

This is optional but worth it. Once set up, my code changes deploy automatically to your URL — no more ZIP drag-and-drop.

### Step 2.1 — Create a GitHub account (if you don't have one)

1. Go to https://github.com → **Sign up** with your email. Pick a username (e.g. `francoisdotta`). Free.

### Step 2.2 — Create the repo

1. Once logged in, top-right **+** → **New repository**.
2. **Repository name**: `golf-hide-app` (or `laguna-lads-app`).
3. **Public** or **Private** — either works. Public is fine, the app is just a static site.
4. Do NOT add a README, .gitignore, or licence (we want it empty).
5. Click **Create repository**.

You'll land on a page with command-line instructions. **Don't run those** — there's an easier path:

6. On the same page, click **uploading an existing file** (it's a link in the small text mid-page).
7. Drag the entire contents of the `golf-app` folder (index.html, sw.js, manifest.webmanifest, the icons, DEPLOY.md) onto the page.
8. Scroll down, in the commit message box type `Initial upload` and click **Commit changes**.

Your code is now on GitHub.

### Step 2.3 — Connect GitHub to Netlify

1. Go to https://app.netlify.com (log in if not already).
2. **Sites** → **Add new site** → **Import an existing project**.
3. **Deploy with GitHub** → authorise Netlify to access your GitHub.
4. Pick the `golf-hide-app` repo you just created.
5. Build settings: leave everything empty (this is a static site, no build step). Publish directory: `.` (just a dot, meaning the root).
6. Click **Deploy**.

After ~30 seconds you get a URL like `https://glowing-pixie-1234.netlify.app`. That's your live app.

### Step 2.4 — Optional polish

- Rename the site: Netlify dashboard → **Site configuration** → **Change site name** → e.g. `laguna-lads`. URL becomes `https://laguna-lads.netlify.app`.
- Custom domain: Netlify dashboard → **Domain management** → **Add a domain you already own**. Walks you through DNS. Works with any domain registrar.

### How updates work from now on

- I push a code change to GitHub.
- Netlify notices within seconds and auto-deploys.
- Your iPhone's PWA picks up the new version next time you open the app.
- No ZIPs. No drag-and-drop.

### Send me your GitHub username

So I can either:
- Push code on your behalf (you give me access — easier), or
- Send you snippets and you upload them via the GitHub web UI (slower but you keep full control).

---

## Summary checklist

- [ ] Supabase project created
- [ ] Project URL + anon key copied and sent to me
- [ ] 3 SQL tables created
- [ ] Admin user created in Supabase Auth
- [ ] GitHub account created (if needed)
- [ ] GitHub repo created with golf-app contents uploaded
- [ ] Netlify site connected to the GitHub repo
- [ ] GitHub username sent to me

Once that's all done, I do the final wiring (admin login flow + edit screens in Match Play and OOM), push it, and you can update results live from your phone.
