# Xfce 4.18 Backports for Linux Mint 22.1 (Xia / Ubuntu 24.04 Noble)

Targeted stability, security, and bug-fix backports for Xfce 4.18 core components, panel plugins, and desktop apps on Linux Mint 22.1 (Ubuntu 24.04 Noble base).

All patches in `patches/` were verified directly against the Ubuntu Noble / Linux Mint Xia source packages, built with `dpkg-buildpackage -us -uc -b`, and verified on a live X11 Xfce 4.18 session.

---

## Patched Packages Summary (20 Packages)

### Round 1 — Core Xfce 4.18 Components

| Package | Local Version | Upstream Base / Fixes | Patch File(s) |
|---|---|---|---|
| `xfce4-power-manager` | `4.18.4+mint1~local2` | 4.18.4 + Mint tray icon + regenerated `xfpm-settings_ui.h` + 5 post-4.18.4 upstream fixes + 6 local audit fixes | `patches/xfce4-power-manager.patch` |
| `thunar` | `4.18.11+mint1~local2` | 4.18.11 + Mint patches + 12 backported 4.20.x crash/UAF fixes | `patches/thunar.patch`, `patches/thunar-local2.patch` |
| `xfce4-panel` | `4.18.6-1ubuntu0.1~local2` | 4.18.6 + tasklist NULL windows guard (`27eebdfe`) | `patches/xfce4-panel.patch`, `patches/xfce4-panel-local2.patch` |
| `xfce4-session` | `4.18.4-1build2~local2` | 4.18.4 + preview cairo fix + `save_idle_id` UAF fix (`cad43525`) | `patches/xfce4-session.patch`, `patches/xfce4-session-local2.patch` |
| `xfce4-settings` | `4.18.6-0ubuntu3~local1` | 4.18.6 (display cairo/profile, RandR, 4.18.5 regression reverted) | `patches/xfce4-settings.patch` |
| `libxfce4util` | `4.18.2-2build3~local1` | 4.18.2 + branch MD5 thumbnail path fix (`e76cbf70`) | `patches/libxfce4util.patch`, `patches/libxfce4util-branch.patch` |
| `libxfce4ui` | `4.18.6-1build4~local1` | 4.18.6 (shortcut grabber, SM client ICE reset) | `patches/libxfce4ui.patch` |
| `garcon` | `4.18.2-1build3~local1` | 4.18.2 (menu loading UAF, symlinked desktop files) | `patches/garcon.patch` |
| `xfconf` | `4.18.3-1build3~local1` | 4.18.3 + unreleased `get_properties()` empty hash fix (`ea37c072`) | `patches/xfconf.patch` |
| `tumbler` | `4.18.2-1.1build4~local1` | 4.18.2 + branch gepub/path/fatal-log fixes | `patches/tumbler.patch`, `patches/tumbler-branch.patch` |

### Round 2 — Remaining Core Components

| Package | Local Version | Fixes | Patch File |
|---|---|---|---|
| `xfwm4` | `4.18.0-1build3+local1` | 16 upstream stability fixes (`XChangeProperty` 64-bit buffer overflow, alt-tab UAF `#762`, IO watch UAF, NULL `cwindow_hash`, uninitialized vars, leaks) | `patches/xfwm4.patch` |
| `xfdesktop4` | `4.18.1-1build3+local1` | Remove pending thumbnailer `request_timer_id` in `dispose()` | `patches/xfdesktop.patch` |
| `xfce4-appfinder` | `4.18.0-1build2+local1` | Off-by-one stack overflow in `fuzzy_match()` | `patches/xfce4-appfinder.patch` |
| `thunar-volman` | `4.18.0-1build2+local1` | Missing `{ NULL }` terminator in `option_entries` | `patches/thunar-volman.patch` |
| `exo` | `4.18.0-1build4+local1` | `cairo_save`/`restore` imbalance, leaks, URI handling (`#108`) | `patches/exo.patch` |

### Round 3 — Active Desktop Apps & Panel Plugins

| Package | Local Version | Fixes | Patch File |
|---|---|---|---|
| `xfce4-notifyd` | `0.9.4-1+local1` | D-Bus `image_data` integer overflow + `<=0` bounds/ref cleanup (`7cf9114b`), odd `actions` infinite loop (`472697a7`), disconnected monitor NULL/OOB (`14bb3cc6`), icon cache `GFileEnumerator` NULL check (`79059bd7`) | `patches/xfce4-notifyd.patch` |
| `xfce4-pulseaudio-plugin` | `0.4.8-1build2+local1` | `g_hash_table_lookup` NULL key crash (`1a86726b`), numerous `pa_operation` leaks (`23ff03b8`) | `patches/xfce4-pulseaudio-plugin.patch` |
| `xfce4-terminal` | `1.1.3-1build1+local1` | Working dir NULL guard (`acb40c61`), `tabs_menu` NULL + weak pointer (`b8b2fb4d`), tab activity CPU throttle (`4efd886c`), hyperlink pcre2/uri leaks (`8461299f`) | `patches/xfce4-terminal.patch` |
| `xfce4-screenshooter` | `1.10.5-1build1+local1` | NULL `g_str_equal()` segfaults (`93a3f4f2` `#132`, `bb3df99c`) | `patches/xfce4-screenshooter.patch` |
| `xfce4-dict` | `0.8.6-1build2+local1` | `lib/dictd.c` heap-buffer-overflow (`238440c8`), NULL deref (`630053c2`) | `patches/xfce4-dict.patch` |

---

## Detailed Fix Highlights

### `xfce4-power-manager` (`4.18.4+mint1~local2`)
- **4.18.4 upstream fixes**: aggregate charge state (`#109`), brightness key ungrab (`#33`), `pm-helper` `GError` init, systray scroll events, Mint `ac-adapter-symbolic` default preserved.
- **Build-pipeline bug fixed**: regenerated stale `data/interfaces/xfpm-settings_ui.h` via `xdt-csource` (previously skipped when `--disable-maintainer-mode` was active) and updated `settings/xfpm-settings.c` critical-level validation from `> 20` to `> 70` so `#144` and the `"Handle brightness keys"` default actually take effect.
- **Post-4.18.4 upstream backports**: `brightness-exponential` listener (`a5523291`), multi-inhibitor D-Bus disconnect cleanup (`0329b291` + keeping monitor active while sibling cookies remain), enabled `raw` backlight preference (`c30b8c88` `#74`), dialog Esc/close response handling (`fc860da8` on both inhibit and lock-failure dialogs), backlight dimming/OSD percentage calculation with min/max guards (`76d03a6e`).
- **Source-audit fixes**: `"can-reboot"` → `"can-restart"` with initialized boolean in `xfpm_power_dbus_can_reboot()`, `g_variant_unref(NULL)` guard in `xfpm-systemd.c`, 60s Polkit timer UAF in `xfpm_polkit_finalize()`, `time_to_full = to_full` in `xfpm-battery.c`, `inhibit_fd = -1` init/reset in `xfpm-manager.c`, cancel `set_level_timeout` on popup destroy in `power-manager-button.c`.

### `thunar` (`4.18.11+mint1~local2`)
- **4.18.9–4.18.11**: symlink resolver/thumbnailer (`#1260`), infinite symlink reload loop (`#1270`), split-view/DnD/undo/redo fixes.
- **12 backported 4.20.x crash/UAF fixes**: location button removal (`#1507`, `e57be768`), location bar (`#1539`, `a90f9316`), uninitialized accel map (`#1488`, `7a5faa99`), properties dialog NULL deref (`#1585`, `73db8d6a`), `thunar_file_accepts_drop` NULL unref (`d64f35c9`), window destruction UAF (`#1593`, `d22b6890`), renamer dialog UAF (`#1458`, `0829f835`), rename dialog underlying change UAF (`f81cbb2d`), unmount `shortcut->location` missing ref (`#1778`, `1378530e`), unknown gvfs UAF/double-unref (`#1834`, `b6f01254`), unmounted reload crash (`#1799`, `51ac9b1f`), gvfs connect crash (`#1450`, `fcc1e02c`).

---

## Versioning & Build Notes

- **Higher upstream version** (e.g. `4.18.4+mint1~local2`, `4.18.11+mint1~local2`): uses `~localN` so an official distro update to the same upstream version cleanly supersedes the local build.
- **Same upstream version** (e.g. `0.9.4-1+local1`, `4.18.0-1build3+local1`): uses `+local1` so `apt` sorts it newer than the installed repo package.
- **Applying & building**:
  ```bash
  # Fetch source package (via apt-get source or mirror pool .dsc + dpkg-source -x)
  patch -p1 < /path/to/patches/<package>.patch
  # For xfce4-power-manager, ensure xfpm-settings_ui.h is up to date if xfpm-settings.ui changed:
  # xdt-csource --static --strip-comments --strip-content --name=xfpm_settings_ui data/interfaces/xfpm-settings.ui > data/interfaces/xfpm-settings_ui.h
  dch -l +local1 "Backport upstream fixes"
  dpkg-buildpackage -us -uc -b
  ```

## Rollback

Check repository versions with `apt-cache policy <pkg>` and reinstall the official package version, e.g.:

```bash
sudo apt-get install --allow-downgrades xfce4-notifyd=0.9.4-1
```
