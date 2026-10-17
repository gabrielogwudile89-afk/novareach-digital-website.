
create table if not exists public.listings (
  id uuid primary key default gen_random_uuid(),
  seller_id uuid not null references auth.users(id)
    on delete cascade,
  title text not null
    check (char_length(title) between 3 and 120),
  description text not null
    check (char_length(description) <= 5000),
  price numeric(12,2) not null check (price > 0),
  listing_type text not null
    check (listing_type in ('product', 'service')),
  status text not null default 'active'
    check (status in ('draft', 'active')),
  created_at timestamptz not null default now()
);

alter table public.listings enable row level security;

create index if not exists listings_status_created_idx
on public.listings (status, created_at desc);

create policy "Anyone can view active listings"
on public.listings
for select
to anon, authenticated
using (status = 'active' or seller_id = auth.uid());

create policy "Sellers can create their own listings"
on public.listings
for insert
to authenticated
with check (seller_id = auth.uid());

create policy "Sellers can update their own listings"
on public.listings
for update
to authenticated
using (seller_id = auth.uid())
with check (seller_id = auth.uid());

create policy "Sellers can delete their own listings"
on public.listings
for delete
to authenticated
using (seller_id = auth.uid());



