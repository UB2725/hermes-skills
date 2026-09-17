# XML views (developer mode) — works on Community

Source: https://www.odoo.com/documentation/17.0/applications/studio/views.html

Activate developer mode: Settings -> scroll to **Developer Tools** -> **Activate the developer
mode**. Developer mode unlocks advanced tools and the Technical menu. Treat it as advanced: risky
settings are reachable from there.

To edit a view's XML: open the view you want to edit, select the **View** tab, click **</> XML**.

IMPORTANT: avoid editing standard and inherited views directly in the XML editor — those changes
are reset and lost during updates or module upgrades. For anything permanent, create an inherited
view in your own module (see `custom-module.md`).

## Inheritance syntax

```xml
<record id="unique_id" model="ir.ui.view">
    <field name="name">human readable name</field>
    <field name="model">the.model.name</field>
    <field name="inherit_id" ref="module.base_view_xml_id"/>
    <field name="arch" type="xml">
        <xpath expr="//field[@name='target_field']" position="attributes">
            <attribute name="invisible">1</attribute>
        </xpath>
    </field>
</record>
```

`position` values: `inside`, `replace`, `before`, `after`, `attributes`, `move`.

You can target a specific element directly instead of using xpath:

```xml
<field name="phone" position="attributes">
    <attribute name="invisible">1</attribute>
</field>
```

## Recipes

### Hide a column in a list view

```xml
<field name="expected_revenue" position="attributes">
    <attribute name="invisible">1</attribute>
</field>
```

### Rename a field label (without touching the database)

```xml
<field name="partner_id" position="attributes">
    <attribute name="string">Nama Jamaah</attribute>
</field>
```

### Make a field required only in this view

```xml
<field name="phone" position="attributes">
    <attribute name="required">1</attribute>
</field>
```

### Reorder: move a field next to another

```xml
<xpath expr="//field[@name='phone']" position="move" target="name"/>
```

### Add a button to a form header

```xml
<xpath expr="//header" position="inside">
    <button name="action_kirim_wa" string="Kirim WhatsApp" type="object" class="btn-primary"/>
</xpath>
```

### Add a new page (tab) to a form's notebook

```xml
<xpath expr="//notebook" position="inside">
    <page string="Data Jamaah">
        <group>
            <field name="nomor_paspor"/>
            <field name="tanggal_keberangkatan"/>
        </group>
    </page>
</xpath>
```

### Limit a field to a user group

```xml
<field name="margin" position="attributes">
    <attribute name="groups">base.group_system</attribute>
</field>
```

## Finding the correct inherit_id

1. Enable developer mode.
2. Settings -> Technical -> Views. Search by model name; the External ID column is the value for `ref=`.
3. Alternatively open the target view, click **</> XML**, and read the view's own external id / name.
4. Odoo resolves a priority-ordered *set* of views for a model. Inherit the most specific view that
   actually renders the element you want to change.

Common base view naming: `<module>.view_<model>_form`, `<module>.view_<model>_tree`,
`<module>.<model>_view_kanban`, `<module>.view_<model>_search`.

## Verify your change took effect

- Restart with `-u <your_module>` to force an update, then reload the UI.
- If the xpath finds nothing, Odoo logs a warning and silently skips the whole arch — a missing
  change is usually a wrong `expr` or wrong `inherit_id`, not a caching issue.
- Use Settings -> Technical -> Views to confirm your inherited view is listed and active.
