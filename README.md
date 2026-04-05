# 8th Wall + three.js — tap to place

Web AR sample: tap the ground plane to spawn a scaled-up `tree.glb` with tweened animation. It uses **8th Wall Engine** (SLAM / world tracking), **three.js**, **XRExtras** (loading UI, fullscreen canvas, errors), and **tween.js**.

Integration follows the [8th Wall Engine overview](https://8thwall.org/docs/engine/overview) (Camera Pipeline Module API + `XR8.run`).

## Requirements

- **HTTPS** in the browser (camera access needs a [secure context](https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts)). Use the included dev server or your own TLS setup.
- A **WebGL-capable** browser; for phone AR, use a supported mobile browser as described in 8th Wall’s docs.

## Run locally

```bash
npm install
npm run serve:https
```

On first run, the script generates `cert.pem` and `key.pem` (self-signed, ignored by git via `*.pem` in `.gitignore`). The server picks a free port and prints URLs such as `https://127.0.0.1:<port>`. For a **fixed port**, set `PORT` (example: `PORT=8443 npm run serve:https`).

To regenerate certificates only:

```bash
npm run cert
```

### Mobile testing

Use the **LAN IP** shown in the terminal (not `127.0.0.1`). You may need to trust the self-signed certificate on the device. See the [Engine overview — Test on Mobile](https://8thwall.org/docs/engine/overview) section for HTTPS notes.

## GitHub Pages

This repo ships a static site (`index.html` and assets at the root). Deploy with **GitHub Actions**:

1. In the GitHub repo, open **Settings → Pages**.
2. Under **Build and deployment**, set **Source** to **GitHub Actions** (not “Deploy from a branch”).
3. Push to `main` (or run the workflow manually under **Actions**). The workflow file is [`.github/workflows/deploy-github-pages.yml`](.github/workflows/deploy-github-pages.yml).

After a successful run, the site is available at:

`https://<your-username>.github.io/<repository-name>/`

Example: `https://tkada.github.io/8thwall_threejs_placeground/`

Relative asset paths (`index.js`, `tree.glb`) work on project Pages without extra configuration. A [`.nojekyll`](.nojekyll) file disables Jekyll so nothing strips or mis-serves static files.

**Note:** AR still needs a supported browser and may be subject to 8th Wall / camera policies on third-party origins; GitHub Pages provides HTTPS, which satisfies the usual secure-context requirement for camera access.

## Project layout

| File        | Role |
| ----------- | ---- |
| `index.html` | Loads tween.js, XRExtras, Engine `xr.js` (`data-preload-chunks="slam"`), import-mapped three.js, and deferred `index.js`. |
| `index.js`   | Camera pipeline modules (`GlTextureRenderer`, `Threejs`, `XrController`, XRExtras helpers, custom `placeground` module). |
| `index.css`  | Full-viewport `body` / `#camerafeed`. |
| `tree.glb`   | Model spawned on tap. |

## Behavior

On **touchstart**, a `THREE.Raycaster` hits a horizontal shadow plane at Y=0. A hit loads the GLB via `GLTFLoader`, places it with random Y rotation, and scales it up with **tween.js**. Two-finger tap calls `XR8.XrController.recenter()`.

## References

- [8th Wall Engine — Overview](https://8thwall.org/docs/engine/overview)
- [Camera Pipeline Module API](https://8thwall.org/docs/api/engine/camerapipelinemodule) (linked from the overview)

Legacy hosted-template demo (may differ from this repo): [templates.8thwall.app/placeground-threejs](https://templates.8thwall.app/placeground-threejs)
