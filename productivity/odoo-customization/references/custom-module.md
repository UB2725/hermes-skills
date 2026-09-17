# Custom module skeleton (Community / self-hosted)

Use this when a customization must survive upgrades, or when the same change is needed on
production as well as dev.

## Directory layout

```
<addons_root>/            <- must be listed in addons_path in odoo.conf
└── <company>_ui/
    ├── __init__.py       <- required, can be empty
    ├── __manifest__.py
    ├── models/
    │   └── __init__.py
    ├── views/
    │   └── <model>_views.xml
    ├── security/
    │   └── ir.model.access.csv   <- only if you add new models
    └── static/
        └── src/
            ├── scss/custom.scss
            └── js/custom.js
```

Register in `odoo.conf` (comma-separated):

```ini
addons_path = /path/to/odoo-src/addons, /path/to/odoo-custom
```

## Manifest

```python
{
    'name': 'Berathi UI',
    'version': '17.0.1.0.0',
    'summary': 'Per-app UI overrides for PT Berathi',
    'depends': ['crm', 'sale', 'account', 'project'],
    'data': [
        'views/crm_lead_views.xml',
        'views/sale_order_views.xml',
    ],
    'assets': {
        'web.assets_backend': [
            'berathi_ui/static/src/scss/custom.scss',
        ],
    },
    'installable': True,
    'application': False,
    'license': 'LGPL-3',
}
```

`depends` must list every module whose base views you override, otherwise the `inherit_id ref=`
will not resolve and your arch is skipped.

## Applying changes

- First install: Apps -> Update Apps List -> search your module -> Install.
- After editing XML: upgrade the module. Either Apps -> your module -> Upgrade, or restart Odoo
  with `-u <module_name>`.
- Python model changes always need an Odoo restart; pure XML changes only need the module upgrade.
- Log line to watch: the Odoo logfile. A bad xpath logs a warning and drops your entire arch.

## Branding / theme (global, not per-module)

- Logo and company name: Settings -> Companies -> upload logo.
- Colors and backend look: ship SCSS via the `web.assets_backend` bundle as above.
- Prefer CSS variables/classes over `!important` overrides. The Odoo 17 backend uses OWL components,
  so brittle selectors break between minor versions.

## Dev -> production flow

1. Build and test in the dev DB. Never experiment on the production database.
2. `pg_dump` the production DB before deploying a new module version.
3. Copy the module folder to the server's addons root, then upgrade the module there.
4. Verify in the UI; keep the previous module folder until confirmed working.

For a laptop dev setup, keep PostgreSQL and Odoo start/stop manual via a small control script rather
than always-on services. Restart pattern: stop Odoo, then run
`<venv>/bin/python odoo-bin -c odoo.conf -u <module>` to apply changes, then start normally.
