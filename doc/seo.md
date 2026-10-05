# SEO

## Meta Tags, Schema and Protecting dev

## The Pattern at a Glance

### Terms (consistent across the whole site)
* **Category:** always `Open Source Private Cloud`, never `Open Source Infrastructure`.
* **Lab:** the free lab to rebuild and try out.
* **Workshop Slides:** the slides that go with the workshop. "Workshop" as a product of socket GmbH is not presented as an offer on the site, the site is a free collection of content.
* **Cheat Sheet:** two words, not "Cheatsheet".

### Title Scheme
Every title ends with ` | infraguide.org`, only About carries the name at the front. Length up to about 60 characters.

| Page type | Pattern | Example |
|---|---|---|
| Home page | `Open Source Private Cloud Infrastructure` | as pattern |
| Overview page | `Open Source Private Cloud <Type>` | `Open Source Private Cloud Labs` |
| Lab page | `<Product> Lab: <Benefit>` | `OpenStack Lab: Bare Metal to Private Cloud` |
| Slides | `<Product> Workshop Slides` | `Ceph Workshop Slides` |
| Cheat sheet | `<Product> Cheat Sheet: <Topics>` | `Ceph Cheat Sheet: orch, rbd, rados, CephFS` |
| About | `About infraguide.org \| Open Source Private Cloud` | as pattern |

New products (Incus, Proxmox, ...) and new sections (Guides, Articles, Tools) follow the same scheme, for example `Incus Lab: ...` and `Open Source Private Cloud Guides`.

### Description Rules
* **Overview pages:** categories instead of product names (cloud, virtualization, storage). That way they don't go stale when products are added.
* **Home page:** names all sections that appear there, including those marked "soon". This is a deliberate trade-off.
* **Product pages:** product name plus what the reader gets. Free, no sales tone, no delivery formats like "group or custom delivery".
* **Length:** up to about 155 characters. Language English, no dash as a punctuation mark.

### Headings
* **Home page and overviews:** H1 equals the title without the suffix.
* **Lab pages:** H1 `<Product> Lab`, below it H2 `Lab` and H2 `Workshop slides`.
* **Cheat sheets:** H1 `<Product> Cheat Sheet` plus an introductory sentence. The H1 is missing today.

### Titles of All Pages (each plus ` | infraguide.org`)

| Page | Title |
|---|---|
| `/` | Open Source Private Cloud Infrastructure |
| `/learn/` | Open Source Private Cloud Labs |
| `/learn/openstack/` | OpenStack Lab: Bare Metal to Private Cloud |
| `/learn/ceph/` | Ceph Lab: Deploy and Operate a Cluster |
| `/learn/openstack/slides.html` | OpenStack Workshop Slides |
| `/learn/ceph/slides.html` | Ceph Workshop Slides |
| `/cheat-sheets/` | Open Source Private Cloud Cheat Sheets |
| `/cheat-sheets/openstack/` | OpenStack CLI Cheat Sheet: Common Commands |
| `/cheat-sheets/ceph/` | Ceph Cheat Sheet: orch, rbd, rados, CephFS |
| `/about/` | About infraguide.org \| Open Source Private Cloud (without suffix) |


## Structured Data (JSON-LD)
Home page
```html

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "WebSite",
  "name": "infraguide.org",
  "url": "https://infraguide.org/",
  "inLanguage": "en"
}
</script>
```
