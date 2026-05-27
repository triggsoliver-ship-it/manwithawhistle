# Deploying manwithawhistle to Vercel (with the Ionos domain)

Same workflow as the GenoVaq site: GitHub repo &rarr; Vercel auto-deploys on push &rarr; Ionos domain points at Vercel.

One-time setup is about 15 minutes. After that, every change goes live with `git push`.

There are five parts:

1. Push the site to a new GitHub repo
2. Import the repo into Vercel
3. Add the Ionos domain on Vercel
4. Point Ionos DNS at Vercel
5. Wait for DNS, verify, set primary

I've already done the local Git setup for you — the repo is initialised in this folder with the first commit ready to push.

---

## Part 1 &middot; Push to GitHub (3 minutes)

### 1a. Create the empty repo on GitHub

1. Go to **https://github.com/new**
2. **Repository name:** `manwithawhistle` (or whatever you like)
3. **Visibility:** Private is fine. Public also works.
4. **Don't** tick "Add a README" / "Add .gitignore" / "Add license" &mdash; we already have those locally.
5. Click **Create repository**. GitHub shows a "Quick setup" page with a URL like `git@github.com:yourname/manwithawhistle.git`. Copy that URL.

### 1b. Push the local repo

Open Terminal, `cd` into the project folder, and run:

```bash
cd "/Users/ollytriggs/Documents/Claude/Projects/Owain Website"
git remote add origin git@github.com:YOURNAME/manwithawhistle.git
git branch -M main
git push -u origin main
```

(Replace the URL with the one GitHub gave you. If you use HTTPS instead of SSH, paste the HTTPS URL.)

Refresh the GitHub page &mdash; you should see all the files there.

---

## Part 2 &middot; Import the repo into Vercel (2 minutes)

1. Go to **https://vercel.com/new**
2. Sign in with your GitHub account (same one you pushed to).
3. If prompted, install the **Vercel GitHub app** and grant access to the `manwithawhistle` repo.
4. The repo appears in the list &mdash; click **Import** next to it.
5. On the configuration page:
   - **Framework Preset:** Other (or "Static" / "No Framework" &mdash; Vercel will auto-detect)
   - **Root Directory:** leave as `./`
   - **Build & Output Settings:** leave empty (it's a static site, no build needed)
6. Click **Deploy**.
7. About 15 seconds later, Vercel gives you a live URL like `https://manwithawhistle.vercel.app`. The site is live on that URL right now &mdash; share it with Owain if you want.

From this point on, every `git push` to `main` redeploys automatically.

---

## Part 3 &middot; Add the Ionos domain on Vercel (2 minutes)

In the Vercel project dashboard:

1. Click **Settings** (top nav) &rarr; **Domains** in the left sidebar.
2. In the "Add" box, type the domain Owain bought (e.g. `manwithawhistle.com`) and click **Add**.
3. Vercel asks if you also want to add `www.manwithawhistle.com` &mdash; **yes**, add both. Vercel will redirect one to the other automatically.
4. Vercel now shows both domains with a status of **Invalid Configuration** and a panel showing the DNS records you need to set at Ionos. The values you need are:
   - **A record** for the apex (`manwithawhistle.com`) &rarr; value `76.76.21.21`
   - **CNAME** for `www` &rarr; value `cname.vercel-dns.com`

Keep this Vercel tab open &mdash; copy the values from there in the next part (use whatever values Vercel actually shows, in case they've updated).

---

## Part 4 &middot; Point Ionos DNS at Vercel (5 minutes)

This is the only fiddly bit. We're editing DNS records inside Ionos.

1. Log in at **https://my.ionos.com**
2. Top menu &rarr; **Domains & SSL**
3. Click the domain Owain bought.
4. Click the **DNS** tab (or globe icon).

You'll see a list of existing records. **Don't touch MX records** &mdash; those route email. We only edit A records for `@` and CNAME for `www`.

### Set the apex (`@`) to point at Vercel

1. Find any existing **A record** for host `@` (some interfaces show it as blank). Edit it (or delete it and add a new one).
2. Set:
   - **Type:** A
   - **Host / Name:** `@`  (or blank)
   - **Points to / Value:** `76.76.21.21`
   - **TTL:** 1 hour (default is fine)
3. Save.

### Set `www` to point at Vercel

1. Find any existing **CNAME** for host `www`. Edit or replace it.
2. Set:
   - **Type:** CNAME
   - **Host / Name:** `www`
   - **Points to / Value:** `cname.vercel-dns.com`
   - **TTL:** 1 hour
3. Save.

That's it for Ionos. Log out.

> **Ionos quirks:**
> - If you can't edit records, look for a setting called **"Use my own DNS settings"** or similar and switch to that &mdash; some Ionos plans default to a locked "Ionos DNS" mode. Don't change the nameservers themselves; just enable record editing.
> - If Ionos complains "AAAA record exists, remove it first" &mdash; delete the AAAA record for `@` then add the A record.
> - **Leave the MX records alone.** They handle Owain's email. Deleting them = email stops working.

---

## Part 5 &middot; Wait, then verify (anywhere from 5 minutes to a few hours)

DNS propagation takes its own time. Usually 5&ndash;30 minutes; occasionally a few hours. You can't speed it up.

1. Back in Vercel &rarr; Settings &rarr; Domains, the "Invalid Configuration" warning turns into a green tick once DNS resolves. Refresh occasionally.
2. Once the green tick appears, Vercel auto-provisions a Let's Encrypt SSL certificate within a few more minutes.
3. Open `https://manwithawhistle.com` (or whatever the real domain is) in a private/incognito window. It should load the site over HTTPS.

### Check propagation manually

If you're impatient:

- **https://dnschecker.org** &rarr; paste the domain &rarr; type **A** &rarr; you want to see `76.76.21.21` showing across most regions.
- Terminal: `dig +short manwithawhistle.com` &rarr; when it returns `76.76.21.21`, you're live.

### Set the primary domain

1. In Vercel &rarr; Settings &rarr; Domains, click the three dots next to `manwithawhistle.com` (the apex) &rarr; **Set as primary domain**.
2. The `www` version will now automatically 308-redirect to the apex. (If Owain prefers `www` as primary, set that as primary instead.)

You're done. The site is live on the real domain with HTTPS, and every `git push` redeploys it automatically.

---

## Updating the site (the normal workflow from now on)

```bash
cd "/Users/ollytriggs/Documents/Claude/Projects/Owain Website"
# ... edit index.html or add files ...
git add .
git commit -m "Add Bracken's training reel"
git push
```

Vercel sees the push and redeploys in about 10 seconds. The domain stays the same. No DNS, no zip, no clicking around.

For experimenting with changes, push a branch instead of `main`:

```bash
git checkout -b try-new-photo
# ... make changes ...
git push -u origin try-new-photo
```

Vercel builds a **preview deployment** on a unique URL for that branch, so you can show Owain before merging.

---

## Troubleshooting cheat sheet

| Symptom | Likely cause | Fix |
|---|---|---|
| Domain shows Ionos parking page | DNS still propagating, or A record didn't save | Wait 30 min, re-check Ionos DNS: `@` &rarr; A &rarr; `76.76.21.21` |
| "Your connection is not private" / SSL error | DNS resolved before Vercel finished SSL | Wait 10&ndash;15 minutes; Vercel issues the cert automatically |
| `www` works but apex doesn't (or vice versa) | One DNS record was set, the other wasn't | Recheck both `@` (A) and `www` (CNAME) at Ionos |
| Vercel says "Invalid Configuration" forever | Wrong DNS values, or AAAA record still pointing to Ionos | Compare values exactly to what Vercel's domain page shows. Delete stray AAAA records on `@` |
| Email stopped working | MX records were deleted by accident | In Ionos DNS, restore the original MX records (Ionos has a default-reset option for email-only) |
| `git push` is rejected | Repo isn't connected, or auth failed | Re-run `git remote -v` to check the URL. Set up SSH key or use a Personal Access Token over HTTPS |
| Pushed but Vercel didn't deploy | Vercel GitHub app doesn't have access to this repo | Vercel &rarr; Settings &rarr; Git &rarr; reinstall app and grant the repo |

---

## Alternative: host directly on Ionos (not recommended)

If Owain ever insists on hosting on Ionos itself: log into Ionos &rarr; Hosting &rarr; My Hosting Plans &rarr; open the package &rarr; File Manager. Upload `index.html` and the `assets/` folder into the web root (`htdocs/` or `public_html/`). Connect the domain to the hosting plan and enable Let's Encrypt SSL. It works but you lose the auto-deploy-on-push and preview-URL workflow &mdash; not worth it.
