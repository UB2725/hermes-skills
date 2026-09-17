# Access, users, and apps — the no-code route

Sources:
- https://www.odoo.com/documentation/17.0/applications/general/users.html
- https://www.odoo.com/documentation/17.0/applications/general/apps_modules.html

Before writing any XML to "make a module look different for someone", check whether the real need is
access control.

## Users

- Add individual users, set the **User type** (which controls the default access groups).
- Deactivate instead of deleting users who leave — historical records reference them.
- Password management: reset from the login page (if enabled), send reset instructions, or change it
  directly as an admin.

Per-user module visibility in practice: revoke the app's access rights / groups for that user, and
Odoo hides the app and its menus for them. This is the correct way to "only show certain modules to
the right people" — no view inheritance needed.

Multi-company: users can be given access to several companies; record visibility follows the company
selector. Useful when one Odoo instance serves several legal entities.

## Apps and modules

- Install: Apps -> search -> Install. Enable developer mode to see technical module names.
- Upgrade: Apps -> the module -> Upgrade. Required after changing a module's XML/Python.
- Uninstall: removes the module's data too. Take a backup first — uninstall is not a soft toggle for
  modules that created records.
- Update Apps List is required to make a brand-new module folder visible to Odoo.

## Rule of thumb

| Goal | Route |
|---|---|
| User should not see an app at all | Access rights / groups |
| User should see the app but not certain fields | View inheritance with `groups` attribute |
| Everyone should see a different layout | View inheritance, no groups |
| Only disable Create/Edit/Delete buttons | View-level settings (Studio) or `create`/`edit`/`delete` attributes |
