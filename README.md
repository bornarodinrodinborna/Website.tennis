# RODIN.SPORT — Astro site

Landing page for Borna Rodin's tennis placement business, rebuilt in [Astro](https://astro.build).

## What changed from the original HTML file

- **All embedded media extracted.** The original file had the hero video and 5 photos embedded as base64 text, making it 11MB. They're now real files in `public/assets/`, and the video has been re-compressed (8MB → 2.5MB) with `ffmpeg`. Total page weight dropped from 11MB to ~3MB.
- **Booking**: the "Book a Free Assessment" button in the contact section now opens a **Calendly** popup instead of just scrolling to an anchor.
- **Contact form**: added a real form (name, email, phone, player age, message) wired to **Formspree**, so inquiries land in your inbox as structured emails — no backend needed.
- Added `<meta description>`, Open Graph tags, and a favicon for link previews and SEO basics.

## Before you deploy — two things to configure

Open `src/pages/index.astro` and edit the two constants at the top of the file:

```astro
const CALENDLY_URL = "https://calendly.com/YOUR-CALENDLY-USERNAME/30min";
const FORMSPREE_ID = "YOUR_FORMSPREE_ID";
```

### 1. Calendly
1. Create a free account at [calendly.com](https://calendly.com).
2. Create an event type (e.g. "30-Minute Assessment Call") and set your availability.
3. Copy its booking link (looks like `https://calendly.com/your-name/30min`) and paste it into `CALENDLY_URL`.

### 2. Formspree
1. Create a free account at [formspree.io](https://formspree.io).
2. Create a new form — it'll give you an ID that looks like `xyzabcde`.
3. Paste just that ID into `FORMSPREE_ID` (not the full URL).
4. Formspree will ask you to confirm your email address the first time a submission comes in — do that so future messages land directly in your inbox.

## Local development

Requires [Node.js](https://nodejs.org) 18 or newer.

```bash
npm install
npm run dev
```

Open the URL it prints (usually `http://localhost:4321`) to preview the site. Changes to `src/pages/index.astro` hot-reload automatically.

To produce the production build locally (output goes to `dist/`):

```bash
npm run build
npm run preview
```

## Deploying — GitHub + Azure Static Web Apps

### Step 1: Push this project to GitHub

```bash
cd astro-site
git init
git add .
git commit -m "Initial commit — RODIN.SPORT Astro site"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPO.git
git push -u origin main
```

(Create the empty repo on GitHub first if you haven't — github.com → New repository.)

### Step 2: Create the Azure Static Web App

1. Go to the [Azure Portal](https://portal.azure.com) → **Create a resource** → search **Static Web App** → **Create**.
2. Fill in:
   - **Subscription / Resource group**: your existing ones (or create a new resource group).
   - **Name**: e.g. `rodin-sport`.
   - **Plan type**: **Free** is enough for a single landing page.
   - **Deployment details** → **Source**: **GitHub** → sign in and authorize Azure → pick your organization, the repo you just pushed, and branch `main`.
   - **Build details**:
     - **Build presets**: choose **Astro** if listed, otherwise **Custom**.
     - **App location**: `/`
     - **Api location**: *(leave blank)*
     - **Output location**: `dist`
3. Click **Review + create** → **Create**.

Azure will automatically:
- Add a `AZURE_STATIC_WEB_APPS_API_TOKEN` secret to your GitHub repo, and
- Commit its own GitHub Actions workflow file into `.github/workflows/`.

**This repo already includes a workflow file** at `.github/workflows/azure-static-web-apps.yml`. If Azure adds a second one during setup, that's fine — either delete the duplicate Azure adds, or delete the one included here and keep Azure's. Only one is needed; having both will just run the deploy twice on every push.

### Step 3: Confirm it deployed

- Go to the **Actions** tab of your GitHub repo — you should see a workflow run in progress, then green.
- Go back to the Azure Static Web App resource → the **URL** field at the top (something like `https://<random-name>.azurestaticapps.net`) is your live site.

### Step 4 (optional): Custom domain

In the Azure Static Web App resource → **Custom domains** → **Add** → follow the instructions to point your domain's DNS (a CNAME or TXT + A record, Azure tells you exactly which) at the app. Free SSL is provisioned automatically once DNS validates.

## Every future update

```bash
git add .
git commit -m "describe your change"
git push
```

Pushing to `main` automatically triggers the GitHub Action, which rebuilds and redeploys to Azure — typically live within 1–2 minutes.

## Testing checklist before sending real traffic

- [ ] Replaced `CALENDLY_URL` and `FORMSPREE_ID` with your real values
- [ ] Submitted the contact form once yourself to confirm the email arrives
- [ ] Clicked the Calendly button to confirm the popup opens and a real slot can be booked
- [ ] Checked the site on a phone (this design is responsive, but always verify on a real device)
- [ ] Confirmed the custom domain (if added) shows the padlock/HTTPS
