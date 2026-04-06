# Project Guidelines

## Architecture
- This repository is a static site exported from a WordPress/Financity setup. Pages are stored as directory-based routes with `index.html` files at the root and in folders such as `services/`, `portfolio/`, `company-profile/`, and `gallery/`.
- Root-level page directories are the site pages an agent will usually edit. Preserve the directory-per-page structure when adding or fixing routes.
- Theme and plugin assets exist under both `assets/` and `wp-content/`. Before editing CSS or JS, verify which path the current page actually loads.

## Editing Conventions
- Check the live HTML for the page you are editing before changing shared assets. Do not assume `assets/css/*` or `assets/js/*` is the active source; many pages load `wp-content/themes/financity/*` and `wp-content/uploads/financity-style-custom.css`.
- Preserve relative paths carefully. This site uses many depth-sensitive links to `wp-content/`, `wp-includes/`, and sibling page folders.
- Keep fixes minimal and targeted. Do not reformat whole HTML files unless necessary.
- Treat exported WordPress remnants as part of the runtime. Avoid removing `wp-content`, `wp-includes`, slider, or plugin references unless you have confirmed replacements.

## Validation
- There is no formal build or test pipeline in this workspace.
- Validate changes by scanning affected HTML for broken relative paths, duplicated attributes, and inconsistent script tags.
- Use the existing redirect map in [redirects.json](../redirects.json) when changing route names or page destinations.
- Use [nav-consistency-report.csv](../nav-consistency-report.csv) as a reference artifact for the normalized first-party navigation labels.

## Project-Specific Pitfalls
- Multiple similar copies of theme JS exist, including [assets/js/script-core.js](../assets/js/script-core.js) and [wp-content/themes/financity/js/script-core.js](../wp-content/themes/financity/js/script-core.js). Confirm which one the page loads before editing.
- Some pages may exist outside this workspace in mirrored or local-server copies. Changes in this repo will not affect those external copies.
- `services/index.html` and other nested pages are sensitive to relative path depth for emoji, jQuery, slider, and asset URLs.
- Navigation fixes often require checking both desktop and mobile menu markup in the same HTML file.