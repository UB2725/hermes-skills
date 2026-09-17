# hermes-skills

Kumpulan skill untuk [Hermes Agent](https://hermes-agent.nousresearch.com/docs) — procedural memory
yang dimuat otomatis saat tugas yang relevan muncul.

Setiap skill adalah satu folder berisi `SKILL.md` (frontmatter + isi) dan opsional
`references/`, `templates/`, `scripts/`, atau `assets/`.

## Daftar skill

| Skill | Kategori | Kegunaan |
|---|---|---|
| [odoo-customization](productivity/odoo-customization/) | productivity | Customize tampilan Odoo 17 per modul — Studio, XML views, modul custom |

## Struktur

```
<kategori>/<nama-skill>/
├── SKILL.md            # wajib
└── references/         # opsional: dokumentasi pendukung per topik
```

## Cara pakai

Salin folder skill ke direktori skill Hermes:

```bash
cp -R productivity/odoo-customization ~/.hermes/skills/productivity/
```

Hermes memuat `SKILL.md` hanya ketika deskripsinya cocok dengan tugas yang sedang dikerjakan,
sehingga isi skill tidak memakan konteks saat tidak relevan. Detail panjang sengaja ditaruh di
`references/` agar hanya terbaca saat benar-benar dibutuhkan.

## Sumber

- `odoo-customization` disusun dari dokumentasi resmi Odoo 17
  ([applications](https://www.odoo.com/documentation/17.0/applications.html),
  [Studio](https://www.odoo.com/documentation/17.0/applications/studio.html),
  [developer mode](https://www.odoo.com/documentation/17.0/applications/general/developer_mode.html)),
  dilengkapi catatan untuk Odoo Community self-hosted yang tidak punya Studio.