# Field types and non-default widgets

Source: https://www.odoo.com/documentation/17.0/applications/studio/fields.html

Fields structure models. A field's data presentation and formatting on the UI is controlled by its
**widget**. Studio offers 20 choices across 15 technical field types because some types are offered
more than once with a different default widget.

## Simple fields

### Text (`char`) — short text, one line

Non-default widgets:

- **Badge** — value shown in a rounded tag shape; not editable on the UI but a default value can be set.
- **Copy to Clipboard** — copy via a button.
- **E-mail** — value becomes a clickable mailto link.
- **Image** — displays an image from a URL. The image is NOT stored in Odoo, so it saves disk space
  compared to a real Image field. Value not manually editable; a default can be set.
- **Phone** — clickable tel link. Optional **Enable SMS** adds a send-SMS action next to the field.
- **URL** — clickable URL.

### Multiline Text (`text`) — longer text, two lines

- **Copy to Clipboard**.

### Integer (`integer`) — positive, negative, or zero, no decimals

- **Percentage Pie** — shows the value inside a pie/progress indicator.

### Decimal (`float`)

Numeric with decimals.

### Monetary (`monetary`)

Amount tied to a currency field; needs a currency field on the model.

### Html (`html`)

Rich text edited with the Odoo editor.

### Date (`date`) / Date & Time (`datetime`)

Date & Time fields using the **Date & Time** or **Date Range** widget expose additional
date-specific properties.

### Checkbox (`boolean`)

True/false.

### Selection (`selection`)

Fixed list of choices. Variants: **Priority** (star rating) and other widget-specific renders.

### File (`binary`) / Image (`binary`) / Sign (`binary`)

Binary storage. **Image** renders as a picture; **Sign** captures a signature.

## Relational fields

### Many2One (`many2one`) — link to one record of another model

### One2Many (`one2many`) — list of records on the other side; the **Lines** widget renders it as an
editable embedded list (order lines, invoice lines)

### Many2Many (`many2many`) — links to many records; the **Tags** widget renders it as a tag picker

### Related field (`related`) — value pulled from a related record

## Adding a field to a view (summary)

- **New field**: Add tab -> drag from **New Fields** -> drop -> configure in **Properties**.
  Activate developer mode first so the Technical Name is editable. The Technical Name cannot be
  changed after creation.
- **Existing field**: Add tab -> **Existing Fields** -> drag onto the view.
- Properties available: Label, Widget, per-group visibility, Invisible, Required, Readonly.
  These can be set independently for different views.
- **Remove from view**: Properties tab -> bottom -> Remove from view. Reversible.
  For pure decluttering, prefer Invisible over removing.
