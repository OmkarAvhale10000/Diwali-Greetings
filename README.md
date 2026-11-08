# Diwali Celebration Universe (Node)

A 5-step Diwali journey. Visitors place stars to build a constellation, see a live count of
universes, visit friends' universes, and download or share a poster with a greeting.

- Zero dependencies, Node 18 or newer
- Live updates through Server-Sent Events
- Greetings written by Claude when `ANTHROPIC_API_KEY` is set, otherwise a built-in greeting

## Run locally
```
cp .env.example .env     # optional: add your Anthropic API key
export $(grep -v '^#' .env | xargs)   # or just: ANTHROPIC_API_KEY=sk-... npm start
npm start                # http://localhost:3000
```

## Deploy
The start command is `npm start`. Set `PORT` if your host doesn't (most do).

- **Render / Railway / Koyeb:** create a Web Service from your Git repo, build command empty
  (or `npm install`), start command `npm start`. Add `ANTHROPIC_API_KEY` as an environment variable.
  Add a persistent disk and set `DATA_DIR` to its mount path (for example `/var/data`).
- **Fly.io:** `fly launch`, create a volume, mount it, and set `DATA_DIR` to the mount path.
- **VPS:** `npm i -g pm2 && pm2 start server.js --name diwali`, then put Nginx or Caddy in front.
  For Nginx, keep `proxy_buffering off;` on `/api/stream` so live updates flow.

## Important notes
- Universes are stored in `DATA_DIR/universes.json`. Without a persistent disk they reset on redeploy.
- Run a single instance. For several instances, replace the `db` Map in `server.js` with Redis or
  Postgres, and use pub/sub to broadcast updates.
- Serverless hosts (Vercel/Netlify functions) can't keep SSE connections or local files. Use a
  long-running Node host as above.
- Your API key stays on the server and is never sent to the browser.
- Anonymous visitors can add universes. Limits built in: 15 saves per IP per hour, 10 stars each,
  names cleaned to 20 safe characters, 5,000 stored universes (oldest dropped).
