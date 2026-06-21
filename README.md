# Headless WordPress Starter

A Next.js 16 starter that uses WordPress as a headless CMS. The home page loads the WordPress page with the slug `home`, and other URLs are resolved through the catch-all route in `app/[...slug]/page.jsx`.

## Prerequisites

Install these before starting:

- [Node.js](https://nodejs.org/) 20.9 or newer
- npm (included with Node.js)
- A WordPress site with REST API access

## Step 1: Install the project

Clone or download the project, open a terminal in its folder, and install the dependencies:

```bash
npm install
```

## Step 2: Configure WordPress

Make sure WordPress has a published page whose slug is `home`. That page is displayed at the frontend root URL (`/`).

WordPress's standard REST API should also be available. Test it in a browser:

```text
https://your-wordpress-site.com/wp-json/wp/v2/pages
```

This project also requests global block styles from:

```text
https://your-wordpress-site.com/wp-json/headless/v1/global-styles
```

If that custom endpoint is not installed, pages will still load, but the generated WordPress global styles will be empty.

## Step 3: Create the environment file

Create `.env.local` in the project root:

```env
WORDPRESS_API_URL=https://your-wordpress-site.com/wp-json/wp/v2
NEXT_PUBLIC_WORDPRESS_API_URL=https://your-wordpress-site.com
NEXT_PUBLIC_SITE_URL=http://localhost:3000
```

What each variable does:

- `WORDPRESS_API_URL` is the server-side WordPress REST API base URL.
- `NEXT_PUBLIC_WORDPRESS_API_URL` is the WordPress site root. It is used for WordPress styles and for converting backend links to frontend links.
- `NEXT_PUBLIC_SITE_URL` is the public URL of this Next.js site.

The existing `WORDPRESS_HAG_KEY` and `WORDPRESS_HAG_SECRET` variables are not currently used by the application. Only add them if you later implement authenticated WordPress requests.

Do not commit `.env.local`; it is already ignored by Git.

## Step 4: Allow WordPress images

Open `next.config.mjs` and set the HTTPS hostname to your WordPress domain:

```js
images: {
  remotePatterns: [
    {
      protocol: "https",
      hostname: "your-wordpress-site.com",
    },
    {
      protocol: "http",
      hostname: "localhost",
    },
  ],
},
```

Use only the hostname—do not include `https://` or a path. Restart the development server after changing this file.

## Step 5: Start development

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

- `/` loads the WordPress page with the slug `home`.
- `/about` loads the WordPress page with the slug `about`.
- Other paths are handled by `app/[...slug]/page.jsx`.

Changes to WordPress REST data are cached for one hour. Global WordPress styles are refreshed every 60 seconds.

## Step 6: Check the project

Run the linter:

```bash
npm run lint
```

Create a production build:

```bash
npm run build
```

Run the production build locally:

```bash
npm run start
```

## Project structure

```text
app/
  page.js                 Home page (`home` WordPress slug)
  [...slug]/page.jsx      Dynamic WordPress pages
  layout.js               Site layout and WordPress styles
src/
  lib/wordpress-server.js Server-side WordPress requests and caching
  lib/wordpress.jsx       Backend-to-frontend link conversion
  lib/WordpressContent.jsx WordPress HTML rendering and client navigation
  scss/                   Global Sass styles
next.config.mjs           Next.js and remote image configuration
.env.local                Local environment variables (not committed)
```

## Common problems

### Home page not found

Confirm that WordPress has a published page with the exact slug `home` and that `WORDPRESS_API_URL` ends with `/wp-json/wp/v2`.

### WordPress request failed

Open the REST API URL directly and confirm it returns JSON. Also check the environment variable for spelling, trailing spaces, and the correct protocol.

### Invalid image hostname

Add the image's hostname to `remotePatterns` in `next.config.mjs`, then restart Next.js.

### WordPress links still open the backend

Set `NEXT_PUBLIC_WORDPRESS_API_URL` to the WordPress site root and `NEXT_PUBLIC_SITE_URL` to the Next.js site URL. Both values should use the correct `http` or `https` protocol.