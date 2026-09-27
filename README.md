# Jeff Clark's Umbrel Community App Store

A personal Umbrel Community App Store, built from Umbrel's
[template](https://github.com/getumbrel/umbrel-community-app-store), for apps
that aren't in the official Umbrel App Store.

Store ID: `jclark` — every app in here must have an id starting with `jclark-`.

## Apps in this store

- **jclark-openviking** — [OpenViking](https://github.com/volcengine/OpenViking),
  a self-hosted context/memory database for AI agents.

## Publishing this store

1. Create a new GitHub repo (public — Umbrel needs to be able to fetch it) and
   push this folder to it. From a terminal with `gh` set up:

   ```bash
   cd jclark-umbrel-app-store
   git init
   git add .
   git commit -m "Initial app store"
   gh repo create jclark-umbrel-app-store --public --source=. --push
   ```

2. On your Umbrel server: Settings → App Store → Add a Community App Store,
   and paste the repo's GitHub URL (e.g.
   `https://github.com/jclark1978/jclark-umbrel-app-store`). Umbrel reads
   `umbrel-app-store.yml` from the repo root, and it'll show up as the
   "Jeff Clark App Store" tab.

3. Install `OpenViking` from that store like any other app.

## OpenViking: one manual step before it's usable

OpenViking's server won't start serving requests until it has a config file
(`ov.conf`) with your embedding/VLM model provider and API key — there's no
setup wizard built into the Umbrel install flow for this, since `ov.conf` is
normally created by running `openviking-server init` interactively.

Two ways to handle it on your Umbrel VM (10.92.201.55):

**Option A — run init inside the running container once**

```bash
ssh umbrel@10.92.201.55
docker exec -it jclark-openviking_server_1 openviking-server init
docker restart jclark-openviking_server_1
```

**Option B — pre-seed the config file before you ever install the app**

Write `ov.conf` yourself (see
[OpenViking's server config docs](https://docs.openviking.net/en/configuration/01-server)
for the field layout: `embedding.dense`, `vlm`, `storage`, `server`), then
copy it onto the Umbrel host at the app's data directory before installing —
typically `~/umbrel/app-data/jclark-openviking/data/ov.conf` — so it's already
there when the container starts.

Either way, once `ov.conf` is in place, the Studio UI is reachable at
`http://10.92.201.55:1933/studio` (or through the app's tile in Umbrel, which
proxies the same port).

## Adding more apps later

Duplicate `jclark-openviking/` as `jclark-<your-app-id>/`, and edit its
`umbrel-app.yml` and `docker-compose.yml`. Keep the `jclark-` prefix on the
folder name and the `id` field in both files — that's what Umbrel uses to
guarantee no collisions with the official store or other community stores.
