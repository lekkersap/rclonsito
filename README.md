# Static pages for a Google OAuth app (GitHub Pages)

Two self-contained pages, no build step, no external assets:

| File | Serves at | Purpose |
| --- | --- | --- |
| `index.html` | `/` (site root) | Application home page |
| `privacy/index.html` | `/privacy/` | Privacy policy |

They are plain HTML with inline CSS, so they work unmodified on GitHub Pages, and they respond to the
visitor's light/dark mode. The only script is one line that writes the current year into the footer.

---

## 1. Before you publish: replace the placeholders

Every placeholder is wrapped in square brackets so it is easy to find. Use your editor's find-and-replace
across **both files**.

| Placeholder | Replace with | Where |
| --- | --- | --- |
| `[App Name]` | Your app's name exactly as it appears on the OAuth consent screen | both |
| `[Short Tagline]` | A few words describing the app | home |
| `[Company Name]` | Your name or registered company name (the data controller) | both |
| `[support@example.com]` | A real, monitored contact address | both |
| `[your-username]` | Your GitHub username or organisation | both |
| `[your-repo]` | The repository name | both |
| `[your-app-url]` | Where the actual application lives | home |
| `[type of application]`, `[target audience]`, `[primary benefit]` | Describe what the app really does | home |
| `[Month DD, YYYY]` | Effective and last-updated dates | privacy |
| `[Country]`, `[Jurisdiction]`, `[Street address, ...]` | Your legal location and postal address | privacy |
| `[X]` | A response-time number, e.g. `5` | both |
| `[30]`, `[90]`, `[24]` | Retention periods that reflect what you actually do | privacy |
| `[Hosting / database / email provider]` | Your real subprocessors, or delete the table row | privacy |
| Square-bracketed guidance paragraphs | Rewrite or delete them — do not publish them as-is | both |

The bracketed sentences in "About", "How it works", the cookie section, the scope list, and the retention
table are guidance, not content. Google's reviewers read these pages, and generic filler is a common cause
of rejection.

### Extra fields this template does not cover

- **Terms of Service** — optional for Google, but the consent screen allows a link. Add `terms/index.html`
  the same way if you want one.
- **In-app account deletion link** — if your app has a settings screen, add that sentence in section 10.

---

## 2. Publish on GitHub Pages

### Option A — browser only (no software to install)

1. Sign in to GitHub and create a new **public** repository. On the free plan, Pages only works on public
   repositories. The name decides your URLs:

   | Repository name | Home page URL | Privacy policy URL |
   | --- | --- | --- |
   | `<username>.github.io` | `https://<username>.github.io/` | `https://<username>.github.io/privacy/` |
   | anything else, e.g. `my-oauth-app` | `https://<username>.github.io/my-oauth-app/` | `https://<username>.github.io/my-oauth-app/privacy/` |

2. On the empty repository page, click the **uploading an existing file** link.

3. Upload the **contents** of the `github-pages` folder — not the folder itself. In File Explorer, open
   `github-pages`, select `index.html`, `README.md` and the `privacy` folder together, and drag them onto the
   drop area.

   Before committing, check the file list above the commit box. It must show:

   ```
   README.md
   index.html
   privacy/index.html
   ```

   If instead you see `github-pages/index.html`, you dragged the folder and the site will end up at the wrong
   address. Remove that entry and drag the individual items instead.

4. Click **Commit changes**.

5. Add the `.nojekyll` marker (optional, but it keeps GitHub from running the files through Jekyll):
   **Add file → Create new file**, name it exactly `.nojekyll`, leave the contents empty, then
   **Commit changes**.

6. Enable Pages: **Settings → Pages → Build and deployment → Source: Deploy from a branch**,
   **Branch: `main`**, **Folder: `/ (root)`**, then **Save**. If the Source dropdown offers *GitHub Actions*
   as the default, switch it to *Deploy from a branch*.

7. Wait for the first deployment, usually under a minute. The Pages settings page shows the live URL once it
   is published. Confirm both addresses load in a private/incognito window (incognito avoids seeing a cached
   404 from before the site existed):
   - `https://<username>.github.io/<repo>/`
   - `https://<username>.github.io/<repo>/privacy/`

To change a file later, open it in the repository, click the pencil icon, edit, and commit.

### Option B — git command line

Install [Git for Windows](https://git-scm.com/download/win) first; it is not currently installed on this
machine, so Option A is the shorter path. Then, from a copy of this folder **outside OneDrive** (OneDrive
syncing a `.git` directory can corrupt the repository):

```bash
git init
git add .
git commit -m "Add app home page and privacy policy"
git branch -M main
git remote add origin https://github.com/<username>/<repo>.git
git push -u origin main
```

Then follow steps 6 and 7 above.

### If you want the privacy policy in its own repository

Move `privacy/index.html` to the root of a second repository and publish it the same way. Both files are
standalone, and the only cross-links are the two relative links (`../` from the policy, `privacy/` from the
home page) — update those to absolute URLs if the two pages end up on different domains.

---

## 3. Configure the Google Cloud OAuth consent screen

In **Google Cloud Console → APIs & Services → OAuth consent screen** (the *Branding* and *Audience* pages):

- **Application home page** → `https://<username>.github.io/<repo>/`
- **Application privacy policy link** → `https://<username>.github.io/<repo>/privacy/`
- **Application terms of service link** → optional
- **Authorized domains** → see the caveat below
- **User support email** and **Developer contact information** → a mailbox you actually read

Google's published requirements that these two pages are built to satisfy
([restricted scope verification](https://developers.google.com/identity/protocols/oauth2/production-readiness/restricted-scope-verification)):

- The home page must be publicly accessible, not behind a login, and its relevance to the app under review
  must be clear.
- The privacy policy must be visible to users, **hosted on the same domain as the home page**, and linked
  on the consent screen. The home page must describe the app's functionality and link to the policy.
- The privacy policy must disclose how the app accesses, uses, stores and shares Google user data, and must
  comply with the [Google API Services User Data Policy](https://developers.google.com/terms/api-services-user-data-policy)
  and its [Limited Use requirements](https://developers.google.com/terms/api-services-user-data-policy#limited-use).
  Section 2 of `privacy/index.html` contains the required Limited Use statement.

### Caveat: `github.io` and the "Authorized domains" field

Google requires you to prove ownership of each authorized domain in
[Search Console](https://search.google.com/search-console/about), and the field expects a registrable
domain. `github.io` is on the Public Suffix List, so `https://<username>.github.io` is frequently rejected
there with a message such as *"must be a top private domain"*, and you cannot add `github.io` itself because
you do not control it.

Practical consequences:

- **Testing / personal use.** Leave the publishing status on **Testing** and add yourself (and any other
  users) as test users. No verification and no authorized domain is required — but users see the
  "Google hasn't verified this app" warning screen, there is a cap on how many accounts can grant access,
  and refresh tokens expire in about 7 days.
- **Public app with sensitive or restricted scopes.** Plan on a domain you own. A custom domain attached to
  the same GitHub Pages site (add a `CNAME` file containing your domain, and DNS records pointing at GitHub)
  solves this, because you can then verify that domain in Search Console and list it as the authorized domain.
  This is the option to choose if you intend to submit for brand or scope verification.
- Basic sign-in scopes (`openid`, `email`, `profile`) are non-sensitive, so an unverified app on `github.io`
  works fine for development and small-scale use.

---

## 4. Final checklist before submitting for verification

- [ ] Every `[...]` placeholder is gone from both files.
- [ ] The home page describes what the app does in plain language, without marketing filler.
- [ ] The home page links to the privacy policy, and the policy links back to the home page.
- [ ] Both pages load over HTTPS with no login required.
- [ ] The contact email in both pages is monitored and matches the consent screen.
- [ ] The privacy policy's retention periods and subprocessor list match reality.
- [ ] The app name on both pages matches the app name on the OAuth consent screen exactly.
