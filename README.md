# House Planner for Unraid

Unraid [Community Apps](https://ca.unraid.net/) template for [House Planner](https://github.com/egmalt/house-planner) by egmalt, a self-hosted house and plot planner with 2D floor plans, a 3D view, a site map and a materials list.

This repository doesn't contain the House Planner source. It contains:

- `templates/house-planner.xml`: the Unraid Docker template.
- `ca_profile.xml`: the Community Apps repository profile.
- `.github/workflows/build.yml`: builds the upstream source into `ghcr.io/jarretraim/house-planner`.

## How updates work

Upstream doesn't publish a container image, so the workflow builds one:

1. Every 6 hours it resolves `egmalt/house-planner@main` to a commit.
2. If `ghcr.io/jarretraim/house-planner:sha-<commit>` doesn't exist yet, it builds upstream's own `Dockerfile` directly from the upstream Git URL at that commit.
3. It starts the container, checks that the app answers and that the auth-protected paths are closed, then pushes `:latest`, `:sha-<commit>`, `:<short-sha>` and `:<package.json version>`.
4. Unraid's Docker tab then shows **update ready** for the container.

To force a rebuild, run the **build** workflow manually with **force** checked.

## Install on Unraid

From Community Apps, search for **house-planner**.

To install before the listing is approved, download the template into Unraid's user templates folder from the Unraid terminal:

```
wget -O /boot/config/plugins/dockerMan/templates-user/my-house-planner.xml \
  https://raw.githubusercontent.com/jarretraim/house-planner-unraid/main/templates/house-planner.xml
```

Then open **Docker → Add Container**, pick **house-planner** from the **Template** dropdown, and apply.

| Setting | Container | Default | Notes |
|---|---|---|---|
| WebUI Port | `8080/tcp` | `8080` | |
| Data | `/var/lib/house-planner` | `/mnt/user/appdata/house-planner` | Plan, versions, snapshots, `config.php` |
| Password | `HOUSE_PASSWORD` | empty | Applied only on first start, when storage is empty. 8+ characters. |
| Setup Key | `HOUSE_SETUP_KEY` | empty | Advanced. Extra key required by `/api/setup.php`. |
| Demo Mode | `HOUSE_DEMO` | empty | Advanced. `1` = no password, plan resets hourly. |

If you leave **Password** empty, open `http://<server>:8080/api/setup.php` once and set a password in the wizard. To change the password later, delete `config.php` from the Data folder and restart the container with a new password, or run the wizard again.

## Support

- Template or image issues: [this repository's issues](https://github.com/jarretraim/house-planner-unraid/issues).
- App bugs and features: [upstream](https://github.com/egmalt/house-planner/issues).

House Planner is MIT licensed by egmalt. The icon is derived from upstream's `favicon.svg`.
