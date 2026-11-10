# Base44 Project

Use this repository to run and edit the app locally, then publish changes back through Base44.

Any change pushed to the repo will also be reflected in the Base44 Builder.

## Prerequisites

1. Clone the repository using the project's Git URL.
2. Navigate to the project directory.
3. Install dependencies: `npm install`.
4. Install the Base44 CLI: `npm install -g base44@latest`.

See the [Base44 CLI docs](https://docs.base44.com/developers/references/cli/get-started/overview) if you want to run Base44 commands directly.

## Run Locally

Run the full local development environment from the project root:

```bash
base44 dev
```

`base44 dev` starts the local Base44 development backend and, when this app is configured for it, also starts the frontend dev server for you. Use the frontend URL printed by the command.

For example, when the Base44 project config includes a `serveCommand`, `base44 dev` can launch the frontend too:

```json5
{
  "site": {
    "serveCommand": "npm run dev"
  }
}
```

In a Base44 project this lives in `base44/config.jsonc`.

## Run Only The Frontend

If you only want to work on the frontend against the hosted Base44 backend, run:

```bash
npm run dev
```

Open the local URL printed by Vite.

## Use The Hosted Backend

For frontend-only development, create or update `.env.local` in the project root:

```bash
VITE_BASE44_APP_ID=your_app_id
VITE_BASE44_APP_BASE_URL=https://your-app.base44.app
```

`VITE_BASE44_APP_ID` identifies the Base44 app.

`VITE_BASE44_APP_BASE_URL` tells the Base44 Vite plugin where to send local `/api` requests. Point it at your deployed Base44 app URL when you want the local frontend to use the hosted backend.

When you use `base44 dev`, the command injects the local Base44 values for you, so `.env.local` is mainly needed for frontend-only workflows.

## Publish Your Changes

After pushing your changes to git, open the Base44 dashboard and publish the app:

```bash
base44 dashboard open
```

## Deploy to Huawei Cloud OBS (Static Hosting)

The frontend builds to portable static files in `dist/` (relative asset paths,
code-splitting, self-hosted favicon/manifest). It can be hosted on a Huawei
Cloud **OBS** bucket with static website hosting enabled, while still calling
the Base44 backend (or a future Huawei Cloud backend) via env vars.

### 1. Configure environment

```bash
cp .env.example .env.production
# Set VITE_BASE44_APP_ID and VITE_BASE44_APP_BASE_URL so the frontend can reach
# the Base44 backend. Leave BASE44_BUILDER unset for a clean standalone build.
```

### 2. Deploy with the included script

```powershell
# Windows (PowerShell)
.\deploy\obs-deploy.ps1 -Bucket your-bucket-name
```
```bash
# macOS / Linux / Git-Bash
./deploy/obs-deploy.sh -b your-bucket-name
```

The script runs `npm run build` then syncs `dist/` to `obs://<bucket>/` with
[obsutil](https://support.huaweicloud.com/utiltg-obs/obs_11_0001.html).
Configure credentials once: `obsutil config -i=<AK> -k=<SK> -e=<endpoint>`.

### 3. Enable static website hosting on the bucket

In the OBS console (Bucket > Settings > Static Website Hosting), set:

| Setting      | Value         |
|--------------|---------------|
| Default page | `index.html`  |
| Error page   | `index.html`  |

Setting the **error page to `index.html`** is what makes client-side routing
work on OBS: unknown paths return `index.html` so the React router can resolve
them. The site is then reachable at
`https://<bucket>.<region>.obs-website.myhuaweicloud.com/`.

### 4. (Later) Switch the backend to Huawei Cloud

When the backend is migrated, point the frontend at the new endpoints without
changing app code:

```bash
VITE_API_BASE_URL=https://your-huawei-apig-endpoint
VITE_BACKEND_PROVIDER=huawei
```

All backend calls go through the centralized layer in `src/api/backend.js`.

## Docs & Support

Documentation: [https://docs.base44.com/Integrations/Using-GitHub](https://docs.base44.com/Integrations/Using-GitHub)

Base44 CLI command reference: [https://docs.base44.com/developers/references/cli/commands/introduction](https://docs.base44.com/developers/references/cli/commands/introduction)

Support: [https://app.base44.com/support](https://app.base44.com/support)
