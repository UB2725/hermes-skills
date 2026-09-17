# hermes-skills

Kumpulan skill untuk [Hermes Agent](https://hermes-agent.nousresearch.com/docs) — procedural memory
yang dimuat otomatis saat tugas yang relevan muncul, sehingga tidak memakan konteks saat tidak dipakai.

Setiap skill adalah satu folder berisi `SKILL.md` (frontmatter + isi) dan opsional
`references/`, `templates/`, `scripts/`, atau `assets/`.

## Daftar skill

| Skill | Kategori | Kegunaan |
|---|---|---|
| [odoo-customization](productivity/odoo-customization/) | productivity | Customize tampilan Odoo 17 per modul — Studio, XML views, modul custom |
| [publishing-skills-to-github](software-development/publishing-skills-to-github/) | software-development | Terbitkan skill/folder ke GitHub tanpa `gh` CLI, plus konvensi bentuk repo skill |
| [hermes-agent-skill-authoring](software-development/hermes-agent-skill-authoring/) | software-development | Menulis `SKILL.md` yang benar: frontmatter, struktur, aturan mutu |
| [creating-user-local-skills](software-development/creating-user-local-skills/) | software-development | Membuat skill lokal milik user dari nol |

## Struktur

```
<kategori>/<nama-skill>/
├── SKILL.md            # wajib
└── references/         # opsional: kedalaman per topik
```

Layout ini sengaja mengikuti konvensi Hermes supaya folder bisa disalin langsung ke
`~/.hermes/skills/` tanpa mengubah path.

## Cara pakai

Salin folder skill ke direktori skill Hermes:

```bash
cp -R productivity/odoo-customization ~/.hermes/skills/productivity/
cp -R software-development/publishing-skills-to-github ~/.hermes/skills/software-development/
```

Hermes memuat `SKILL.md` hanya ketika deskripsinya cocok dengan tugas yang sedang dikerjakan.
Detail panjang ditaruh di `references/` agar hanya terbaca saat benar-benar dibutuhkan.

## Sumber

- `odoo-customization` disusun dari dokumentasi resmi Odoo 17
  ([applications](https://www.odoo.com/documentation/17.0/applications.html),
  [Studio](https://www.odoo.com/documentation/17.0/applications/studio.html),
  [developer mode](https://www.odoo.com/documentation/17.0/applications/general/developer_mode.html),
  [users](https://www.odoo.com/documentation/17.0/applications/general/users.html)),
  dilengkapi catatan untuk Odoo Community self-hosted yang tidak punya Studio.
- Skill `software-development/*` adalah konvensi kerja untuk menulis dan menerbitkan skill Hermes.

## Catatan

Skill yang bersifat spesifik terhadap satu instalasi (hostname, IP, kredensial, layout direktori
pribadi) sengaja **tidak** dimasukkan ke repo publik ini. Simpan yang seperti itu secara lokal.