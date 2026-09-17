# Studio (Enterprise only)

Source: https://www.odoo.com/documentation/17.0/applications/studio.html

Studio is a toolbox to customize Odoo without coding. Per app you can add or modify:

- Fields
- Views
- Models, modules, apps
- Automation rules
- PDF reports
- Approval rules
- Security rules

Access: navigate to the app and model you want to modify, then click **Toggle Studio**
(the `oi-studio` icon in the upper-right corner). Close with **Close** in the upper-right.

## Views in Studio

Views are organized into four categories: general, multiple records, timeline, reporting.

| Category | Types |
|---|---|
| General | Form, Activity, Search |
| Multiple records | Kanban, List |
| Timeline | Calendar, Cohort, Gantt |
| Reporting | Pivot, Graph |

Set a model's default view: Studio -> **Views** -> ellipsis icon next to the view ->
**Set as Default**.

### Form view

- Structure the form by drag & dropping **Tabs and Columns** from the **+ Add** tab.
- The Form view is used when creating and editing records (contacts, sales orders, products).

### Kanban view

Often used to support business flows by moving records across stages, or as a card-based
alternative display.

- **Can Create** — untick to prevent users creating new records.
- **Quick Create** — create records directly in the view using a minimalistic form.
- **Default Group By** — set default grouping.

Note: if a Kanban view exists it is used by default on mobile instead of the List view.

### List view

- **Can Create** / **Can Edit** / **Can Delete** — untick to restrict those actions.
- **When Creating Record** — Add record at bottom / Add record on top / Open form view.
  Choosing the first two prevents users from opening records in Form view from the List view.

### Pivot view

Used to explore and aggregate numeric data interactively, drill down by expanding/collapsing.

- **Access records from cell** — reach all records aggregated under a cell.
- **Column grouping**, **Row grouping - First level**, **Row grouping - Second level** — categorize data.
- **Measures** — which numeric fields to aggregate.
- Optionally display the count of records that produced a cell's aggregate.

## Fields in Studio

There are 15 field types technically in Odoo, but Studio offers 20 choices because some types
appear more than once with a different default widget.

### Adding a new field

1. Go to the relevant form or list view, click **Toggle Studio**.
2. In the **Add** tab, drag the field type from **New Fields** to the desired position.
   If it is a relational field, choose the reciprocal field, model, or related field in the pop-up.
3. In the **Properties** tab, configure the field.

Tip: activate developer mode BEFORE adding a field, so the **Technical Name** is visible and
editable during configuration. The Technical Name cannot be changed after creation.

### Adding an existing field

Use when the field exists on the model but is not on the current view.

1. Toggle Studio on the relevant view.
2. **Add** tab -> **Existing Fields** -> reveals all model fields absent from this view.
3. Drag the field to the desired position.
4. Configure it in the **Properties** tab.

Properties can differ independently per view: **Label**, **Widget**, per-user-group visibility,
and whether the field is **Invisible**, **Required**, or **Readonly**.

Tip: adding a new field to a form or list view saves it to the model and makes it available as an
*existing field* in other views of the same model.

### General field properties

- **Invisible** — hide the field on the UI to declutter. Also applies to related contexts such as
  filters and group-by menus depending on configuration.
- **Required** — user must complete it before proceeding.
- **Readonly** — user cannot modify it.

Date & Time fields using the **Date & Time** or **Date Range** widget expose additional
properties specific to date handling.

### Modifying a field's properties

Toggle Studio -> click the field -> **Properties** tab -> change -> **Close**.

### Removing a field from a view

Toggle Studio -> click the field -> scroll to bottom of **Properties** -> **Remove from view** ->
**Ok**. This is reversible: the field can be added back later via **Existing Fields**.

If the goal is merely to declutter, prefer making the field **Invisible** instead of removing it.

## Models, modules, and apps in Studio

Studio can build an app from scratch. When creating a model it suggests features that add the
corresponding fields/behaviour: Contact details, User assignment, Date & Calendar,
Date range & Gantt, Pipeline stages, Tags, Picture, Lines, Notes, Monetary value, Company,
Custom Sorting, Chatter, Archiving.

Customizations can be exported and imported, which is useful for moving Studio work between
databases.
