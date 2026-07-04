# umaru-caro-config

Remote configuration for the [Caro](https://github.com/chocokage/caro_godot) app. Hosted on GitHub so the app can fetch settings without a dedicated backend.

---

## Force Update (`force-update.json`)

Controls the minimum app version allowed. If a user's app version is below `minVersion`, they see a blocking, non-dismissible dialog that directs them to the store.

### Schema

```json
{
  "minVersion": "1.0.0",
  "iosStoreUrl": "https://apps.apple.com/app/caro/id__APP_ID__",
  "androidStoreUrl": "https://play.google.com/store/apps/details?id=cc.umaru.caro"
}
```

| Field | Description |
|-------|-------------|
| `minVersion` | Semver string (e.g. `"1.2.0"`). Any app version below this is forced to update. |
| `iosStoreUrl` | App Store URL opened when iOS users tap "Update Now". |
| `androidStoreUrl` | Play Store URL opened when Android users tap "Update Now". |

### How to Force an Update

1. Edit `force-update.json` in this repo.
2. Set `minVersion` to the version users **must** be on (e.g. `"1.1.0"`).
3. Commit and push to `main`.
4. All app instances running older versions will show the update dialog on next launch.

### How It Works (Client Side)

- The `ForceUpdate` autoload in the Caro app (`scripts/force_update_checker.gd`) fetches this file on launch from:
  ```
  https://raw.githubusercontent.com/chocokage/umaru-caro-config/main/force-update.json
  ```
- Compares the app's `APP_VERSION` constant against `minVersion` using semver comparison.
- If outdated: shows a blocking `AcceptDialog` that cannot be closed — only the "Update Now" button is available, which opens the store URL.
- If the fetch fails (no internet, timeout), the app continues normally (fail-open).

### Important: Keep APP_VERSION in Sync

When releasing a new version of the app:
1. Update `APP_VERSION` in `scripts/force_update_checker.gd`
2. Update `application/short_version` and `application/version` in `export_presets.cfg`
3. Update `version/name` (Android) in `export_presets.cfg`

---

## Adding New Config

To add more remote config (e.g. maintenance mode, feature flags), create a new JSON file in this repo and fetch it from the app using the same pattern as `force_update_checker.gd`.
