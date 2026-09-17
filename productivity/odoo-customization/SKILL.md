---
name: odoo-customization
description: Customize Odoo 17 UI per module, views, fields.
---

# Odoo 17 Customization (per module & view)

References:
- User docs index: https://www.odoo.com/documentation/17.0/applications.html
- Studio: https://www.odoo.com/documentation/17.0/applications/studio.html
- Developer mode: https://www.odoo.com/documentation/17.0/applications/general/developer_mode.html
- Users: https://www.odoo.com/documentation/17.0/applications/general/users.html

## Decision tree: which route to take

Ask ONE question first: does this Odoo have Studio?

- Enterprise with Studio installed -> use Studio (no code, drag & drop). See `references/studio.md`.
- Community (no Studio) -> developer-mode XML editor for one-off tweaks, and a custom module for
  anything that must survive upgrades. See `references/xml-views.md` and `references/custom-module.md`.
- Just hiding apps/menus per user -> do NOT write code. Use access rights.
  See `references/access-and-users.md`.

Ask a SECOND question: throwaway tweak or permanent?

- Throwaway / testing -> XML editor in developer mode is fine. It edits `ir.ui.view` directly.
- Permanent -> custom module with `inherit_id`. Editing standard/studio views directly is reset and
  lost during updates or module upgrades. This is the single most important rule.

## Core concepts

- Model = table (e.g. `crm.lead`). Field = column. Record = row.
- View = how a model's data is displayed. One model has many views of different types.
- Widget = controls how a field's data is presented/formatted in the UI.
- XML ID format for inheritance: `<module>.<xml_id>`, e.g. `crm.crm_case_tree_view_leads`.

View types by category:

| Category | View types |
|---|---|
| General | Form, Activity, Search |
| Multiple records | Kanban, List, Calendar, Cohort, Gantt |
| Reporting | Pivot, Graph |

Typical per-model view set: `form`, `tree` (list), `kanban`, `search`, `pivot`, `graph`, plus
`activity` where chatter is used.

## Per-module customization patterns

### Change what a LIST view shows
Base view id is usually `<module>.view_<model>_tree` or `<module>.<model>_view_tree`.
Hide a column, add a field, change the list's default ordering.

### Change what a FORM view shows
Tabs and columns come from the `Tabs and Columns` element. Common edits: reorder fields, add a
notebook page, add a header button, change a field's label or widget.

### Change what users can do PER VIEW (no code needed)
List views expose Can Create / Can Edit / Can Delete, plus "When Creating Record" behaviour
(Add record at bottom / on top / Open form view). Kanban exposes Can Create, Quick Create,
Default Group By. Set these before writing any XML if the goal is only restricting actions.

### Restrict a field to certain user groups
Field properties can be configured independently per view: Label, Widget, visibility for certain
user groups, and Invisible / Required / Readonly. Use group-based visibility instead of editing the
base field definition when only one view should differ.

## Pitfalls

- Never edit files under `addons/` of the Odoo source tree. Upgrades wipe them.
- `inherit_id` must point at the real XML ID of the base view. In Community find it via
  Settings -> Technical -> Views, or open the view with debug and inspect.
- `position="attributes"` is the safe way to hide/add an attribute. `position="replace"` on a big
  block is fragile across versions.
- A bad xpath logs a warning and silently drops the whole arch. A missing change is usually a wrong
  `expr` or wrong `inherit_id`, not caching.
- Changing a field's label uses the `string` attribute override, not a rename of the field.
- A field's Technical Name cannot be modified after creation via Studio. Decide it before adding.
- Developer mode is required to see/edit technical names. Activate it BEFORE adding fields.
- Adding a new field to a form/list view saves it to the model and makes it available as an
  "Existing Field" in all other views of the same model.
- "Remove from view" is non-destructive and reversible; use Invisible to declutter more softly.

## Working safely on self-hosted Odoo

Production and staging must not share edits. Standard flow:

1. Develop the module in a local/dev DB.
2. Restart Odoo with `-u <module_name>` (or Apps -> Update Apps List -> Upgrade) to apply.
3. Verify in the UI, then copy the module folder to the server and update again.
4. Take a DB backup (`pg_dump`) before upgrading any custom module in production.

Community notes for adding a custom module:

- Odoo only sees modules in a directory listed in `addons_path`. Add the new folder to `addons_path`
  in `odoo.conf`, comma-separated.
- An empty `__init__.py` at module root is required even with no Python models.
- `depends` must list every module whose base views you override.

## One module or many?

Prefer ONE custom module per business domain (e.g. `acme_ui` depending on `crm`, `sale`,
`account`, `project`) rather than one module per Odoo app. Shared assets (SCSS, JS) and shared
`depends` load once and the upgrade step stays a single `-u`. Split only when different people need
to install/remove a customization independently.

## Reference files

- `references/studio.md` — Studio UI walkthrough: views, fields, widgets, properties (Enterprise).
- `references/xml-views.md` — Developer-mode XML editor + inheritance syntax with real examples.
- `references/custom-module.md` — Custom module skeleton, manifest, assets, upgrade workflow.
- `references/access-and-users.md` — Restricting modules/menus per user without code.
- `references/field-and-widget-types.md` — Field types and their non-default widgets.
