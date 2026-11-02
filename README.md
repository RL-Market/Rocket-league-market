# Rocket League Marked Booking Website

A responsive React + Supabase booking calendar covering **2 Nov 2026 – 31 Dec 2027**.
Every date has six one-hour slots from **16:00 to 22:00**.

## Included
- Shared Supabase database; bookings persist across browsers and devices.
- Realtime updates for every open calendar.
- Database unique constraint prevents simultaneous double bookings.
- Public calendar shows only `AVAILABLE` / `BOOKED`; no customer identity fields exist.
- Private `/admin` page for authenticated admins to cancel bookings.
- Netlify-ready build and SPA routing.

## 1. Create the Supabase database
1. Create a project at https://supabase.com/.
2. Open **SQL Editor**, paste and run `supabase/schema.sql`.
3. In **Project Settings → API**, copy the Project URL and `anon` / publishable key.
4. In **Authentication → Users**, create your admin account.
5. Open the admin user's **app_metadata** and set it to `{"role":"admin"}`.
   This must be app metadata, not user metadata.

## 2. Configure and run locally
```bash
cp .env.example .env
```
Put your Supabase URL and anon key in `.env`, then:
```bash
npm install
npm run dev
```
Open the local URL shown by Vite. The public calendar is `/`; admin sign-in is `/admin`.

## 3. Publish on Netlify
1. Push this folder to a GitHub repository.
2. In Netlify, choose **Add new site → Import an existing project**.
3. Build command: `npm run build`; publish directory: `dist`.
4. Add environment variables `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY`.
5. Deploy. `netlify.toml` includes the SPA rewrite.

## Admin use
Visit `https://YOUR-SITE/admin`, sign in with the Supabase admin user, and cancel a booking from the list. Cancellation deletes the slot record, making the slot available again for everyone.

## Security notes
- The public anon key is designed to be present in browser apps. Never put a Supabase `service_role` key in frontend code.
- Public users can read booking date/time only and create reservations.
- The database stores no names, Discord handles, or email addresses.
- Admin cancellation requires the `admin` role in Supabase **app_metadata**.
- The unique `(booking_date, start_time)` constraint is enforced by PostgreSQL, not just the UI.
- For a high-traffic public launch, add CAPTCHA or an edge-function rate limit to reduce automated spam. The current version does not collect customer contact details or send confirmations.

## Time zone
Slots are stored as local calendar dates and local wall-clock times (`time without time zone`). The interface labels the schedule as local time. If you want a fixed business timezone different from the visitor's local understanding, update the displayed timezone label in `src/main.jsx`.
