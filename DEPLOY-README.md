# Deploying to Apache / cPanel

This build is configured specifically for the **`/AFPL` subfolder**
(e.g. `https://yourdomain.com/AFPL/`). Every asset reference (`_next/`,
`brand/`, `team/`, `video/`) is baked in with that exact prefix at build
time — if you need a different subfolder name or the domain root instead,
this build won't work as-is and needs to be rebuilt with the new path,
not just re-uploaded elsewhere.

It's a complete static export — plain HTML, CSS, JS, and media files.
No Node.js, no build step, no server-side code required to run it.

## Deploy (the files in this folder)

1. Log into cPanel → **File Manager** (or connect via FTP/SFTP).
2. Open `public_html`, then create (or open, if it already exists) a
   folder named exactly **`AFPL`** inside it — so the final path is
   `public_html/AFPL/`.
3. Upload **everything inside this folder** — not the folder itself, its
   *contents* — directly into `public_html/AFPL/`. That means
   `index.html`, `.htaccess`, `_next/`, `brand/`, `team/`, and `video/`
   should all sit directly inside `public_html/AFPL/`, not nested in yet
   another subfolder, and not loose in `public_html/` itself.
4. If your FTP client hides dotfiles by default, make sure `.htaccess`
   actually uploads — it's easy to miss since it's invisible in some file
   managers unless you enable "show hidden files."
5. Visit `https://yourdomain.com/AFPL/`. That's it — no further server
   configuration needed.

The included `.htaccess` handles compression, cache headers, and correct
MIME types for the video file. Most cPanel hosts have the required Apache
modules (`mod_deflate`, `mod_expires`, `mod_headers`) enabled by default;
if a section of it doesn't apply, Apache just skips it rather than erroring.

## What's different from the dev-mode version

- The `/api/chat` server route isn't included — static hosting can't run
  server-side code at all. This costs you nothing today: the chatbot
  already runs entirely client-side by default (`lib/chatResponder.ts`),
  answering from the site's own data with no server needed.
- If you later want the live-Claude-API version of the chatbot, that
  specifically requires a Node.js-capable host (see the alternative below)
  — static file hosting can never run that route, regardless of how it's
  configured.

## Alternative: if your cPanel has "Setup Node.js App"

Some hosts (most CloudLinux-based cPanel setups) let you run the actual
Next.js server instead of a static export — this gets you the live-API
chatbot option, on-demand rendering, and image optimization, at the cost
of needing your host to support it. If you have that option and want to
use it instead, you'd need the *other* project archive (the one with
`app/api/chat/route.ts` and a normal `next.config.ts`, not this static
one) and roughly:

1. cPanel → **Setup Node.js App** → create an application, point it at
   your project folder, select a recent Node.js version.
2. Upload the project source (not this `out` folder — the actual source
   with `package.json`, `app/`, `components/`, etc.).
3. Run `npm install` and `npm run build` via the Node.js App's terminal.
4. Set the startup file / command to run `npm run start`.
5. cPanel wires Apache to reverse-proxy your domain to the Node process
   automatically.

If your host doesn't offer this, the static export above is the correct
and complete choice — it isn't a lesser option, just a different one.
