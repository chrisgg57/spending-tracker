# Spending Tracker

A personal expense tracking app with Supabase cloud sync and magic link auth.

## Deploy to Vercel

### Option A — Vercel CLI (fastest)

```bash
npm i -g vercel
cd spending-tracker
vercel
```

Follow the prompts. Your app will be live at a `.vercel.app` URL in under a minute.

### Option B — GitHub + Vercel dashboard

1. Create a new GitHub repo and push these files:
   ```bash
   git init
   git add .
   git commit -m "initial commit"
   git remote add origin https://github.com/YOUR_USERNAME/spending-tracker.git
   git push -u origin main
   ```
2. Go to [vercel.com](https://vercel.com) → New Project → Import your repo
3. Leave all settings as default → Deploy

### After deploying

Once you have your live URL (e.g. `https://spending-tracker-xyz.vercel.app`):

1. Go to your **Supabase dashboard → Authentication → URL Configuration**
2. Update **Site URL** to your Vercel URL
3. Add your Vercel URL to **Redirect URLs**

That's it — magic link auth will now redirect back to your live app.

## Project structure

```
spending-tracker/
├── index.html      # The entire app (single file)
└── vercel.json     # Vercel routing config
```

## Supabase setup

The app expects these tables:

```sql
-- Expenses
create table expenses (
  id uuid default gen_random_uuid() primary key,
  user_id uuid references auth.users(id),
  amount numeric not null,
  category text not null,
  description text,
  date date not null,
  created_at timestamptz default now()
);

-- Budget
create table budgets (
  user_id uuid references auth.users(id) primary key,
  monthly numeric not null default 0
);

-- Row level security
alter table expenses enable row level security;
alter table budgets enable row level security;

create policy "Users manage their own expenses" on expenses
  for all using (auth.uid() = user_id) with check (auth.uid() = user_id);

create policy "Users manage their own budget" on budgets
  for all using (auth.uid() = user_id) with check (auth.uid() = user_id);
```
