# Glowline.io

Glowline.io is a real-time multiplayer neon territory-capture arena.

Players leave their safe territory, draw glowing exposed trails, return to close loops, claim space, collect motes, and cut rival trails.

## Project layout

```text
glowline/
├── package.json
├── server.js
├── README.md
└── public/
    ├── index.html
    ├── landing.css
    ├── social-card.svg
    ├── sitemap.xml
    ├── robots.txt
    ├── game.html
    └── game.js
```

`server.js`, `package.json`, `game.html`, and `game.js` are the developer's game files and must remain unchanged.

The new landing page is:

- `public/index.html`
- `public/landing.css`
- `public/social-card.svg`
- `public/sitemap.xml`
- `public/robots.txt`

## Requirements

- Node.js 18 or newer
- npm
- A host that supports persistent WebSocket connections

Recommended hosts:

- Render
- Railway
- Fly.io
- Heroku-compatible Node hosts
- A VPS running Node.js

Do not deploy this game to a static-only host unless it is paired with a separate WebSocket server.

## Local installation

Install dependencies:

```bash
npm install
```

Start the server:

```bash
npm start
```

Open:

```text
http://localhost:3000/
```

The branded landing page will load at:

```text
http://localhost:3000/
```

The unchanged game page is available at:

```text
http://localhost:3000/game.html
```

Health check:

```text
http://localhost:3000/health
```

Development mode:

```bash
npm run dev
```

## Environment variables

The server uses:

```text
PORT
```

Most managed hosts automatically provide `PORT`. Do not hard-code a production port.

For local development:

```bash
PORT=3000 npm start
```

On Windows PowerShell:

```powershell
$env:PORT=3000
npm start
```

No database or secret key is required by the current implementation.

## Deployment on Render

1. Create a Git repository containing the project.
2. Push the complete project to GitHub or GitLab.
3. Open Render.
4. Create a new Web Service.
5. Select the repository.
6. Use these settings:

```text
Runtime: Node
Build command: npm install
Start command: npm start
```

7. Select an instance suitable for a persistent Node process.
8. Deploy the service.
9. Open the generated Render URL.
10. Confirm that the landing page loads.
11. Confirm that this endpoint returns JSON:

```text
https://YOUR-RENDER-SERVICE.onrender.com/health
```

12. Click `Play Glowline`.
13. Confirm that the game connects and that the browser WebSocket connection is open.

Render provides HTTPS automatically. Because the client selects `wss://` when the page uses HTTPS, secure WebSockets will use the same host automatically.

## Deployment on Railway

1. Push the project to GitHub.
2. Create a new Railway project.
3. Deploy from the GitHub repository.
4. Railway detects the Node project from `package.json`.
5. Set the start command to:

```bash
npm start
```

6. Generate a public domain in Railway.
7. Open:

```text
https://YOUR-RAILWAY-DOMAIN/health
```

8. Confirm that the response contains:

```json
{
  "ok": true
}
```

9. Test the Play button and multiplayer connection.
10. Add a custom domain after the Railway deployment works.

## Deployment on Fly.io

1. Install the Fly.io CLI.
2. Log in:

```bash
fly auth login
```

3. Launch the application from the project folder:

```bash
fly launch
```

4. Set the internal application port to the port provided by the application configuration.
5. Deploy:

```bash
fly deploy
```

6. Test:

```text
https://YOUR-FLY-APP.fly.dev/health
```

7. Confirm that WebSocket connections remain open while playing.

The application must run as a persistent process. Do not configure it as a one-shot or static-only deployment.

## Health check

The server exposes:

```text
GET /health
```

Example:

```bash
curl https://glowline.io/health
```

Expected response shape:

```json
{
  "ok": true,
  "game": "Glowline.io",
  "rooms": 1,
  "uptime": 42.17
}
```

Configure the hosting provider's health check path as:

```text
/health
```

Use an HTTP GET health check.

## Custom domain and DNS

The final canonical domain is:

```text
https://glowline.io/
```

The domain's availability is unverified. Purchase or register it only after a live registrar confirms that it is available.

After the host provides a custom-domain target:

### Option A: Root domain with an A record

If the host provides a fixed IPv4 address, create:

```text
Type: A
Name: @
Value: HOST_IP_ADDRESS
TTL: Automatic or 3600
```

### Option B: Root domain with ALIAS or ANAME

If the DNS provider supports ALIAS or ANAME records, use the host-provided target:

```text
Type: ALIAS or ANAME
Name: @
Value: HOST_PROVIDED_TARGET
```

### `www` subdomain

Create a CNAME:

```text
Type: CNAME
Name: www
Value: glowline.io
TTL: Automatic or 3600
```

Some hosts require `www` to point to a provider-specific hostname instead. Use the exact value supplied by the hosting provider.

### Important DNS notes

- Do not create multiple conflicting A records for the same host.
- Remove old parking or forwarding records.
- Keep DNS records managed in one DNS provider where possible.
- DNS changes can take several minutes to propagate, but some changes may take up to 24–48 hours.
- Add both `glowline.io` and `www.glowline.io` to the hosting provider if both should work.
- Configure the preferred canonical redirect in the hosting provider or DNS/domain control panel.
- The website should ultimately load at `https://glowline.io/`.

## TLS and WebSockets

Production must use HTTPS.

The browser game automatically chooses:

```text
ws://
```

for local HTTP development and:

```text
wss://
```

for HTTPS production deployments.

The hosting provider must support:

- Persistent Node.js processes
- HTTP upgrade requests
- WebSocket connections
- Long-lived connections
- HTTPS termination

Do not put the WebSocket game behind a proxy configuration that disables upgrade requests.

## SEO URLs

The landing page uses:

```text
https://glowline.io/
```

Canonical URL:

```text
https://glowline.io/
```

Social card:

```text
https://glowline.io/social-card.svg
```

Sitemap:

```text
https://glowline.io/sitemap.xml
```

Robots file:

```text
https://glowline.io/robots.txt
```

All of these URLs assume that `glowline.io` has been successfully registered and connected to the production host.

## Production verification checklist

After deployment:

- [ ] `https://glowline.io/` loads successfully.
- [ ] The page title is `Glowline.io — Neon Territory Arena`.
- [ ] The canonical URL is `https://glowline.io/`.
- [ ] `https://glowline.io/social-card.svg` loads.
- [ ] `https://glowline.io/sitemap.xml` loads.
- [ ] The sitemap uses the official sitemap namespace.
- [ ] `https://glowline.io/robots.txt` loads.
- [ ] `https://glowline.io/health` returns "ok": true.
- [ ] The Play button reveals the game iframe.
- [ ] `/game.html` loads.
- [ ] The game reaches the nickname screen.
- [ ] The WebSocket connection succeeds.
- [ ] A player can join a room.
- [ ] Bots appear in the room.
- [ ] Movement works.
- [ ] Boost works.
- [ ] A loop can be completed.
- [ ] The leaderboard updates.
- [ ] Mobile touch controls work.
- [ ] No browser console errors prevent play.
- [ ] The hosting provider keeps the process running.
