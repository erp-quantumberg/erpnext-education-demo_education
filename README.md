# Demo Education

Demo Education is a Frappe application for presenting an education-management
platform to clients. It currently provides a Vue 3 landing page at the site
root, with a product overview and links to the Frappe login and demo request.
The project also includes a feature and dashboard preview; the areas described
on the landing page are not, by themselves, evidence of implemented backend
workflows or data models.

## Features

- **Public landing page:** A responsive Vue 3 page served at `/` to visitors
  who are not logged in. Logged-in users keep Frappe's normal home-page routing.
- **Education platform overview:** Presents student, instructor, and
  administrator perspectives and describes student, faculty, fees, examination,
  library, timetable, transport, hostel, and analytics feature areas.
- **Demo entry points:** Provides a link to the site's Frappe login page and a
  link to schedule a demo.
- **Frappe integration:** Uses Frappe's website routing and serves compiled
  frontend assets from the app's public assets directory.
- **Frontend development workflow:** Uses Vite for local development and
  production builds.

> **Scope note:** The repository currently contains the public-facing landing
> page and its preview content. Confirm that a feature area is implemented in
> backend code before relying on it as a working system capability.

## Requirements

- A working [Frappe Bench](https://github.com/frappe/bench) environment with a
  Frappe Framework version compatible with this app.
- Python **3.14 or newer**, as declared in `pyproject.toml`.
- Node.js and npm for frontend development and builds. They are not needed just
  to install and run an already-built app.
- A Frappe site on which you have permission to install apps.

## App dependencies

- **Frappe Framework:** Required. Demo Education runs as a Frappe app and is
  installed on a Frappe site managed by Bench.
- **Education:** Required. Install the
  [Frappe Education app](https://github.com/frappe/education) on the site
  before installing Demo Education.
- **ERPNext:** Optional. The current app does not use ERPNext features or
  declare ERPNext as a required app, so you do not need to install ERPNext to
  use Demo Education. If you choose to use ERPNext on the same site, install
  ERPNext on that site separately.

The required app dependency is declared in `required_apps` in
`demo_education/hooks.py`.

## Installation

Install a Frappe-compatible version of the Education app on your Bench and
site first, following its installation instructions. Then run these commands
from your Bench directory. Replace the site name with your own Frappe site:

```bash
bench get-app https://github.com/erp-quantumberg/erpnext-education-demo_education.git --branch main
bench --site <your-site-name> install-app demo_education
```

If the app is already present in `apps/demo_education`, skip `bench get-app`.
Start the Bench processes if they are not already running:

```bash
bench start
```

Open the site root (`/`) to view the public landing page. Sign in through
Frappe to access the authenticated experience configured by your site.

## Setup and development

### Frontend development server

From the app directory inside the Bench (`apps/demo_education`), install the
frontend dependencies and start Vite:

```bash
cd frontend
npm install
npm run dev
```

Vite listens on port `8080` and proxies requests to the Frappe web server using
the Bench's `sites/common_site_config.json`. Open your site's development URL,
for example `http://your-site.test:8080`.

The frontend starter notes that a development site may need
`"ignore_csrf": 1` in its `site_config.json` to avoid CSRF errors while using
the Vite proxy. This setting weakens CSRF protection: use it only on a
disposable or isolated development site, and remove it when it is no longer
needed. Do not enable it on a production site.

### Production frontend build

From the app's `frontend` directory:

```bash
npm run build
```

The build writes assets to `demo_education/public/frontend` and synchronizes
the built HTML entry point to `demo_education/www/index.html`. The latter is
served by Frappe at `/`. Build the frontend again after changing its source so
the checked-in/deployed page references the current generated assets.

### Running app tests

Run the app's Frappe tests on an installed site with:

```bash
bench --site <your-site-name> run-tests --app demo_education
```

## Configuration notes

- The app metadata and Frappe hooks are in `demo_education/hooks.py`.
- Guest home-page routing is provided by
  `demo_education.website.get_website_user_home_page`. Guests see the landing
  page; authenticated users fall through to Frappe's normal home-page
  resolution.
- `demo_education/www/index.py` injects the session CSRF token into the page
  and disables page caching for that response. Do not remove this behavior
  without checking the frontend's CSRF handling.
- Frontend source and Vite configuration are under `frontend/`. The Vite
  configuration expects the app to be located inside a Bench so it can read
  `sites/common_site_config.json`.
- `npm install` must be run in `frontend/`, where `package.json` and the npm
  lockfile are located.
- The frontend and Frappe both serve the root page during development and
  production respectively. Use the Vite server for local frontend work and
  Frappe's site URL for the installed/build output.

## Troubleshooting

### `npm install` reports that no package manifest exists

Make sure your current directory is `apps/demo_education/frontend` before
running `npm install`. The backend app directory does not contain a
`package.json`.

### Vite cannot find `sites/common_site_config.json`

Run the frontend from the app's `frontend/` directory within a Frappe Bench
checkout. The Vite configuration resolves the shared site configuration
relative to that location.

### The Vite page cannot reach Frappe

Start the Bench web server with `bench start`, confirm that the site's host
name resolves locally, and check the web server port in
`sites/common_site_config.json`. If the browser reports a CSRF error, review
the development-only note above; do not disable CSRF protection in production.

### The landing page is missing, stale, or shows broken assets

Confirm that the app is installed on the site, then run `npm run build` from
`frontend/`. The build updates the static assets and Frappe homepage HTML.
Reload the site after the build completes.

### The root URL shows another home page

The landing page is intended for Guest users. Signed-in users follow Frappe's
configured home-page behavior. Check the home-page settings for the affected
user and ensure the app's hooks are loaded if the Guest page is not shown.

### A Frappe command cannot find the app

Run Bench commands from the Bench directory, verify `demo_education` exists
under `apps/`, and check that it is installed on the target site with
`bench --site <your-site-name> list-apps`.

## Contributing

Contributions and bug reports are welcome. Before opening a pull request:

1. Keep changes scoped to the issue and follow the existing Python, Vue, and
   JavaScript patterns.
2. For frontend changes, run `npm run build` from `frontend/`.
3. Run the Frappe app tests on a suitable site:

   ```bash
   bench --site <your-site-name> run-tests --app demo_education
   ```

4. Run the configured pre-commit checks. Install
   [pre-commit](https://pre-commit.com/) if needed, then from the app directory:

   ```bash
   pre-commit install
   pre-commit run --all-files
   ```

The pre-commit configuration includes checks for Python formatting/linting,
JavaScript and Vue formatting/linting, and common file issues.

## License

This project is distributed under the MIT License; see [license.txt](license.txt).
The license file currently contains placeholder copyright year and holder
values. Replace those placeholders with the appropriate details when preparing
an official distribution.
