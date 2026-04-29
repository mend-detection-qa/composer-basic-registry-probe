# basic-registry

## Feature exercised

Simple Composer project with two real Packagist packages (`monolog/monolog ^3.0` and `guzzlehttp/guzzle ^7.5`) to verify that Mend SCA correctly detects all direct and transitive dependencies sourced from the Packagist registry.

## What this probe tests

- Mend reads `composer.lock` (lockfile-driven detection) and resolves all 10 packages.
- Every package must appear with `source = registry` (Packagist).
- No transitive dependencies may be silently dropped.
- Resolved versions must match the lockfile exactly (not the constraint ranges from `composer.json`).

## Expected dependency tree

### Direct dependencies (`require`)

| Package | Version | Source |
|---|---|---|
| `monolog/monolog` | 3.10.0 | registry (Packagist) |
| `guzzlehttp/guzzle` | 7.10.0 | registry (Packagist) |

### Transitive dependencies

| Package | Version | Source | Parent |
|---|---|---|---|
| `psr/log` | 3.0.2 | registry | monolog/monolog |
| `guzzlehttp/promises` | 2.3.0 | registry | guzzlehttp/guzzle |
| `guzzlehttp/psr7` | 2.9.0 | registry | guzzlehttp/guzzle |
| `psr/http-client` | 1.0.3 | registry | guzzlehttp/guzzle |
| `symfony/deprecation-contracts` | 3.6.0 | registry | guzzlehttp/guzzle |
| `psr/http-factory` | 1.1.0 | registry | guzzlehttp/psr7 |
| `psr/http-message` | 2.0 | registry | guzzlehttp/psr7, psr/http-client, psr/http-factory |
| `ralouphie/getallheaders` | 3.0.3 | registry | guzzlehttp/psr7 |

### Summary

- Total packages in lockfile: **10**
- Direct (`require`): **2**
- Transitive: **8**
- Dev packages (`require-dev`): **0**
- All sources: **Packagist registry**

## Mend failure modes exercised

- **Missing transitive deps**: Mend must not silently drop any of the 8 transitive packages.
- **Wrong resolved version**: Mend must report the lockfile version (e.g. `3.10.0`), not the `composer.json` constraint (`^3.0`).
- **Source misclassification**: All packages must be reported as coming from Packagist (registry), not VCS or path.

## Probe metadata

```
pattern:        basic-registry
pm:             composer
lockfile:       composer.lock
php_minimum:    8.1
composer_minimum: 2.6
generated:      2026-04-29
target:         remote
repo:           https://github.com/mend-detection-qa/composer-basic-registry-probe
```