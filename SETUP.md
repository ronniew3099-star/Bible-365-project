# Ronnie's Bible Challenge — setup

This is the branded, responsive Bible-reading app. It keeps the existing 1,189-chapter sequence, flexible catch-up/extra-reading behavior, projected completion estimate, calendar, book progress, and IREAP completion checkbox.

## Current status

- The app is prepared for passwordless email-link sign-in through Supabase.
- Cloud sync is **not active yet**. A Supabase project, its URL/public anon key, database table, and hosted app URL still need to be configured.
- Until cloud sync is configured, progress is stored in the current browser only. Use Settings → Download backup to protect local progress.
- The project is structured as a private-first app. The SQL policies below restrict each account to its own progress row. A broader public rollout would still need additional testing, onboarding, privacy copy, and account-scoped local storage before inviting other users.

## 1. Create a Supabase project

1. Create a project in Supabase.
2. In Project Settings → API, copy the Project URL and the publishable/anon public key. Never put a service-role key in this app.
3. Open the SQL Editor and run `supabase-setup.sql`.
4. In Authentication → URL Configuration, set the Site URL to the eventual hosted app URL and add that URL to the Redirect URLs allow-list.
5. In Authentication → Providers, ensure Email is enabled. Configure the email template as desired; Supabase's email OTP flow sends a sign-in link.

## 2. Host the app

Deploy the contents of this folder as a static site (for example, with Vercel). The deployed site must use HTTPS. Set the Supabase Site URL and Redirect URL to the deployed URL.

## 3. Connect the app

1. Open the hosted app.
2. Go to Settings → Cloud sync.
3. Paste the Supabase Project URL and public anon/publishable key, then save.
4. Enter the email address for the account and select “Email me a sign-in link.”
5. Open the link from the same browser/device. Once signed in, progress can sync through the Supabase table.

## 4. Phone access

Open the hosted HTTPS app in Safari on iPhone, tap Share, then Add to Home Screen. Sign in using the same email link account.

## Security notes

- Use only the Supabase public anon/publishable key in the browser app. Never use the service-role key.
- Keep Row Level Security enabled. The SQL file creates per-user read/insert/update policies.
- Do not invite other users until the multi-account experience has been tested, including separate browser profiles and account switching.
