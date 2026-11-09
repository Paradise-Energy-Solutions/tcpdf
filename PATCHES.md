# Paradise Energy Solutions patches to TCPDF

This repository is a fork of [tecnickcom/TCPDF](https://github.com/tecnickcom/TCPDF),
maintained by Paradise Energy Solutions (PES) for its internal LAMP proposal application.
It is **not** affiliated with or endorsed by Tecnick.com LTD.

TCPDF is licensed under the GNU Lesser General Public License v3 (see `LICENSE.TXT`).
All original copyright notices are retained. As required by LGPL-3.0 / GPL-3.0 section 5(a),
every file modified by PES carries a `// Modified    : Paradise Energy Solutions ...` line in
its header, and every change is listed below with its date.

## Branches and tags

| Branch       | Base             | Purpose                                           |
|--------------|------------------|---------------------------------------------------|
| `pes/6.3.x`  | upstream `6.3.0` | The version LAMP has shipped since 2018, plus PES patches |

Tags use a fourth version segment so Composer accepts them as stable releases:
`6.3.0.1`, `6.3.0.2`, ... (a suffix such as `6.3.0-pes.1` is **not** a valid Composer
version and would be silently ignored). Tags are never moved or deleted; every change gets
a new tag. Consumers pin an exact version:

```json
"repositories": [{ "type": "vcs", "url": "https://github.com/Paradise-Energy-Solutions/tcpdf.git" }],
"require": { "tecnickcom/tcpdf": "6.3.0.1" }
```

The hard-coded `"version"` field was removed from `composer.json` because Composer skips any
VCS tag that does not match it.

## Known advisories

Upstream 6.3.0 predates fixes released in 6.7.4–6.8.0 (CVE-2024-56522, CVE-2024-56521,
CVE-2024-56527, CVE-2024-56519, CVE-2024-51058, CVE-2024-22640, CVE-2024-32489).
Rebasing onto a current upstream release is tracked in LD-934.

## Patch log

### 6.3.0.1

| # | Date | Origin | Files | Change |
|---|------|--------|-------|--------|
| PES-1 | 2026-05-21 | LD-806 | `include/barcodes/datamatrix.php`, `include/barcodes/pdf417.php` | Replace deprecated curly-brace string offsets (`$s{$i}`) with `$s[$i]` in `lookAheadTest()` and `getCompaction()`. No behaviour change. |
| PES-2 | 2026-02-06 | LAMP d5e7f6790 | `tcpdf.php` | Cast to int in `getRawCharWidth()` (`$char = (int)round($char)`) and in the GD alpha gamma cache, to avoid PHP 8.1 implicit float-to-int deprecations. |
| PES-3 | 2023-12-05 or earlier | LAMP 0e2a9dd28 | `tcpdf.php` | `_destroy()`: the loop that `unlink()`s cached image files (`$this->imagekeys`) is disabled because it deleted images LAMP still needed. Side effect: TCPDF temp image files are not removed from `K_PATH_CACHE`. |

Fork metadata (this file, README notice, `composer.json` version field) was added in the same release.

### 6.3.0.2

PHP 8.4 compatibility and TLS hardening, ported from LAMP PR #396 (LD-920). Must remain PHP 8.1-compatible.

| # | Date | Origin | Files | Change |
|---|------|--------|-------|--------|
| PES-4 | 2026-11-03 | LD-920, LAMP #396 (831386109) | `tcpdf.php` | `ImageSVG()`: drop `xml_set_object()` (deprecated in PHP 8.4) and register the SVG XML handlers as callables (`array($this, 'startSVGElementHandler')`, `array($this, 'endSVGElementHandler')`, `array($this, 'segSVGContentHandler')`) instead of method-name strings. No behaviour change. |
| PES-5 | 2026-11-03 | LD-920, LAMP #396 (831386109) | `include/tcpdf_static.php` | `url_exists()` and `fileGetContents()`: remove `curl_close()` (a no-op since PHP 8.0, deprecated in PHP 8.5) and `CURLOPT_BINARYTRANSFER` (a no-op since PHP 5.1.3, deprecated in PHP 8.4). No behaviour change. |

## Files intentionally not in this repository

LAMP's in-tree copy also contained the commercial *Century Gothic* font conversions
(`fonts/gothic*`) and PES logo images. Those are not redistributable / not part of TCPDF
and remain private in the LAMP repository.
