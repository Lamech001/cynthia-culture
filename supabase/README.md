# Supabase setup

The storefront uses the public publishable key; database writes are restricted
by row-level security and the `place_guest_order` database function. Never put a
database password or service-role key in a `VITE_` environment variable.

1. Copy `.env.example` to `.env.local` and set the project URL and publishable
   key.
2. Apply database changes with `npx supabase db push`.
3. In **Supabase Dashboard → Authentication → Sign In / Providers**, enable
   the Email provider so added users can sign in. Keep **Allow new users to
   sign up** disabled. Every manually added Auth user receives the `admin`
   role and can manage the full dashboard.
4. Add users in **Supabase Dashboard → Authentication → Users → Add user**,
   then sign in to the Admin dashboard using that user's email and password.

The migration also promotes existing Auth users to `admin`. Do not enable public
sign-up unless you intend to grant every new registrant full administrator
access.

Guest checkout reuses the customer record for an existing normalized email
instead of inserting a duplicate. Customer contact and delivery details are
also saved in the browser for checkout prefill and account access. Profile
changes update the database using the device's order-access token; apply the
latest migration before enabling profile editing in a deployed app.

Promotions created in the Admin dashboard are stored in `public.promotions`.
Active, in-date offers that have not reached their usage limit are shown in
the storefront announcement and as selectable codes during checkout. Customers
can enter a code manually as well; the order RPC rechecks its validity,
minimum subtotal, and usage limit before saving the order. Leave the expiry
date blank for a promotion with no expiry.

The Admin Products section loads all rows from `public.products`, including
hidden products. Admins can create and edit customer-facing product details,
hide products from the storefront, and restore hidden products. Hiding is a
soft delete so existing order history remains intact. The storefront only
loads active products.
