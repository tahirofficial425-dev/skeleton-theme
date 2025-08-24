base_dir = "/mnt/data/lumo-nest-starter-theme"
paths = ["assets","config","layout","locales","sections","snippets","templates"]
for p in paths:
    os.makedirs(os.path.join(base_dir, p), exist_ok=True)

# layout/theme.liquid
open(os.path.join(base_dir,"layout","theme.liquid"),"w").write("""
<!doctype html>
<html lang="{{ request.locale.iso_code }}">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>{{ page_title }}{% if shop.name %} – {{ shop.name }}{% endif %}</title>
  <meta name="description" content="{{ page_description | default: shop.description | escape }}">
  {{ content_for_header }}
  {{ 'base.css' | asset_url | stylesheet_tag }}
</head>
<body class="lumo-nest{% if settings.enable_rtl %} rtl{% endif %}">
  <a class="skip-link" href="#MainContent">Skip to content</a>
  {% section 'header' %}
  <main id="MainContent">{{ content_for_layout }}</main>
  {% section 'footer' %}
</body>
</html>
""".strip())

# assets/base.css
open(os.path.join(base_dir,"assets","base.css"),"w").write("""
:root{--color-bg: {{ settings.background | default: '#ffffff' }};--color-text: {{ settings.text_color | default: '#111' }};--color-accent: {{ settings.accent | default: '#1A73E8' }};--container:1200px;--radius:16px}
*{box-sizing:border-box}body{margin:0;font-family:-apple-system,BlinkMacSystemFont,'Inter',Roboto,Arial,sans-serif;background:var(--color-bg);color:var(--color-text)}
.container{max-width:var(--container);margin:0 auto;padding:0 16px}.button{display:inline-block;padding:12px 18px;border-radius:999px;background:var(--color-accent);color:#fff;font-weight:600}
.header{position:sticky;top:0;background:var(--color-bg);border-bottom:1px solid #eee;z-index:10}.header__inner{display:flex;align-items:center;justify-content:space-between;padding:12px 0}
.hero{padding:56px 0}.hero .hero-card{border-radius:var(--radius);overflow:hidden;background:#f6f7f9;display:grid;grid-template-columns:1.2fr .8fr;align-items:center}.hero .copy{padding:36px}
.product-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:16px}.card{border:1px solid #eee;border-radius:var(--radius);overflow:hidden;background:#fff}.card .img{aspect-ratio:1/1;background:#fafafa;display:grid;place-items:center}
.footer{margin-top:48px;border-top:1px solid #eee;background:#fafafa}.footer__inner{padding:24px 0;display:grid;grid-template-columns:1fr 1fr 1fr;gap:16px}
.rtl{direction:rtl}@media(max-width:960px){.hero .hero-card{grid-template-columns:1fr}.product-grid{grid-template-columns:repeat(2,1fr)}.footer__inner{grid-template-columns:1fr}}
""".strip())

# sections/header.liquid
open(os.path.join(base_dir,"sections","header.liquid"),"w").write("""
<section class="header">
  <div class="container header__inner">
    <div class="logo">
      {% if settings.logo != blank %}
        <a href="{{ routes.root_url }}"><img src="{{ settings.logo | image_url: width: 200 }}" alt="{{ shop.name }}" style="height:36px"></a>
      {% else %}
        <a href="{{ routes.root_url }}">{{ shop.name }}</a>
      {% endif %}
    </div>
    <nav class="nav" role="navigation">
      {% for link in linklists[settings.main_menu].links %}
        <a href="{{ link.url }}">{{ link.title }}</a>
      {% endfor %}
    </nav>
    <div class="header-actions">
      <a href="{{ routes.search_url }}" aria-label="{{ 'general.search.search' | t }}">🔍</a>
      <a href="{{ routes.cart_url }}" aria-label="{{ 'sections.header.cart' | t }}">🛒 ({{ cart.item_count }})</a>
    </div>
  </div>
</section>
{% schema %}
{
  "name": "Header",
  "settings": [
    { "type": "image_picker", "id": "logo", "label": "Logo" },
    { "type": "link_list", "id": "main_menu", "label": "Main menu", "default": "main-menu" }
  ]
}
{% endschema %}
""".strip())

# sections/footer.liquid
open(os.path.join(base_dir,"sections","footer.liquid"),"w").write("""
<footer class="footer" role="contentinfo">
  <div class="container footer__inner">
    <div>
      <h4>{{ 'sections.footer.about' | t }}</h4>
      <p>{{ settings.footer_blurb | default: 'Smart living gadgets curated by Lumo Nest.' }}</p>
    </div>
    <div>
      <h4>{{ 'sections.footer.links' | t }}</h4>
      <ul>
        {% for link in linklists[settings.footer_menu].links %}
          <li><a href="{{ link.url }}">{{ link.title }}</a></li>
        {% endfor %}
      </ul>
    </div>
    <div>
      <h4>{{ 'sections.footer.newsletter' | t }}</h4>
      {% form 'customer' %}
        <input type="email" name="contact[email]" placeholder="{{ 'sections.footer.email' | t }}" required>
        <button class="button" type="submit">{{ 'sections.footer.subscribe' | t }}</button>
      {% endform %}
    </div>
  </div>
  <div class="container" style="padding:12px 0;opacity:.7">&copy; {{ 'now' | date: '%Y' }} {{ shop.name }}</div>
</footer>
{% schema %}
{
  "name": "Footer",
  "settings": [
    { "type": "text", "id": "footer_blurb", "label": "Footer blurb", "default": "Smart living gadgets curated by Lumo Nest." },
    { "type": "link_list", "id": "footer_menu", "label": "Footer menu", "default": "footer" }
  ]
}
{% endschema %}
""".strip())

# sections/hero-banner.liquid
open(os.path.join(base_dir,"sections","hero-banner.liquid"),"w").write("""
<section class="hero">
  <div class="container">
    <div class="hero-card">
      <div class="copy">
        <h1>{{ section.settings.heading }}</h1>
        <p>{{ section.settings.subheading }}</p>
        {% if section.settings.button_label and section.settings.button_link %}
          <a class="button" href="{{ section.settings.button_link }}">{{ section.settings.button_label }}</a>
        {% endif %}
      </div>
      {% if section.settings.bg_image %}
        <img src="{{ section.settings.bg_image | image_url: width: 1200 }}" alt="{{ section.settings.heading | escape }}">
      {% endif %}
    </div>
  </div>
</section>
{% schema %}
{
  "name": "Hero Banner",
  "settings": [
    { "type": "image_picker", "id": "bg_image", "label": "Right image" },
    { "type": "text", "id": "heading", "label": "Heading", "default": "Elevate your home tech" },
    { "type": "textarea", "id": "subheading", "label": "Subheading", "default": "Smart, minimalist gadgets for modern living." },
    { "type": "url", "id": "button_link", "label": "Button link" },
    { "type": "text", "id": "button_label", "label": "Button label", "default": "Shop now" }
  ],
  "presets": [{ "name": "Hero Banner" }]
}
{% endschema %}
""".strip())

# sections/featured-collection.liquid
open(os.path.join(base_dir,"sections","featured-collection.liquid"),"w").write("""
<section class="container" style="padding:24px 0">
  <h2 style="margin:0 0 12px">{{ section.settings.title }}</h2>
  {% assign collection = collections[section.settings.collection] %}
  {% if collection and collection.products_count > 0 %}
    <div class="product-grid">
      {% assign limit = section.settings.rows | times: section.settings.columns %}
      {% for product in collection.products limit: limit %}
        <div class="card">
          <a class="img" href="{{ product.url }}">
            {% if product.featured_image %}
              {{ product.featured_image | image_url: width: 600 | image_tag: alt: product.title }}
            {% else %}<span>Image</span>{% endif %}
          </a>
          <div class="content">
            <a href="{{ product.url }}">{{ product.title }}</a><br>
            {% render 'price', product: product %}
            <div style="margin-top:8px">
              {% if product.available %}
                <form action="/cart/add" method="post">
                  <input type="hidden" name="id" value="{{ product.selected_or_first_available_variant.id }}">
                  <button class="button" type="submit">{{ 'products.product.add_to_cart' | t }}</button>
                </form>
              {% else %}
                <span>{{ 'products.product.sold_out' | t }}</span>
              {% endif %}
            </div>
          </div>
        </div>
      {% endfor %}
    </div>
  {% else %}
    <p>{{ 'sections.featured_collection.empty' | t }}</p>
  {% endif %}
</section>
{% schema %}
{
  "name": "Featured collection",
  "settings": [
    { "type": "text", "id": "title", "label": "Heading", "default": "Featured gadgets" },
    { "type": "collection", "id": "collection", "label": "Collection" },
    { "type": "range", "id": "columns", "min": 2, "max": 4, "step": 1, "default": 4, "label": "Columns" },
    { "type": "range", "id": "rows", "min": 1, "max": 3, "step": 1, "default": 1, "label": "Rows" }
  ],
  "presets": [{ "name": "Featured collection" }]
}
{% endschema %}
""".strip())

# sections/main-product.liquid
open(os.path.join(base_dir,"sections","main-product.liquid"),"w").write("""
<section class="container" style="padding:24px 0">
  <div class="grid" style="grid-template-columns:1fr 1fr;gap:24px">
    <div>
      {% if product.media.size > 0 %}
        {{ product.featured_media | media | default: product.featured_image | image_url: width: 900 | image_tag }}
      {% endif %}
    </div>
    <div>
      <h1>{{ product.title }}</h1>
      {% render 'price', product: product %}
      <div>{{ product.description }}</div>
      <form method="post" action="/cart/add" style="margin-top:16px">
        {% unless product.has_only_default_variant %}
          {% for option in product.options_with_values %}
            <label>{{ option.name }}</label>
            <select name="options[{{ option.name }}]">
              {% for value in option.values %}
                <option value="{{ value | escape }}">{{ value }}</option>
              {% endfor %}
            </select>
          {% endfor %}
        {% endunless %}
        <input type="hidden" name="id" value="{{ product.selected_or_first_available_variant.id }}">
        <button class="button" type="submit">{{ 'products.product.add_to_cart' | t }}</button>
      </form>
    </div>
  </div>
</section>
{% schema %}
{
  "name": "Main product",
  "settings": []
}
{% endschema %}
""".strip())

# sections/main-collection.liquid
open(os.path.join(base_dir,"sections","main-collection.liquid"),"w").write("""
<section class="container" style="padding:24px 0">
  <h1>{{ collection.title }}</h1>
  {% if collection.description != blank %}<p>{{ collection.description }}</p>{% endif %}
  <div class="product-grid">
    {% for product in collection.products %}
      <div class="card">
        <a class="img" href="{{ product.url }}">
          {% if product.featured_image %}
            {{ product.featured_image | image_url: width: 600 | image_tag: alt: product.title }}
          {% endif %}
        </a>
        <div class="content">
          <a href="{{ product.url }}">{{ product.title }}</a><br>
          {% render 'price', product: product %}
        </div>
      </div>
    {% else %}
      <p>{{ 'collections.general.no_matches' | t }}</p>
    {% endfor %}
  </div>
</section>
{% schema %}
{
  "name": "Main collection",
  "settings": []
}
{% endschema %}
""".strip())

# snippets/price.liquid
open(os.path.join(base_dir,"snippets","price.liquid"),"w").write("""
<span class="price">
  {% if product.compare_at_price > product.price %}
    <span class="was-price">{{ product.compare_at_price | money }}</span>
  {% endif %}
  <span class="current-price">{{ product.price | money }}</span>
</span>
""".strip())

# templates
open(os.path.join(base_dir,"templates","index.json"),"w").write(json.dumps({
  "sections": {
    "hero": {"type":"hero-banner","settings":{}},
    "feat": {"type":"featured-collection","settings":{}}
  },
  "order": ["hero","feat"]
}, indent=2))

open(os.path.join(base_dir,"templates","product.json"),"w").write(json.dumps({
  "sections": {"main":{"type":"main-product","settings":{}}},
  "order": ["main"]
}, indent=2))

open(os.path.join(base_dir,"templates","collection.json"),"w").write(json.dumps({
  "sections": {"main":{"type":"main-collection","settings":{}}},
  "order": ["main"]
}, indent=2))

# config/settings_schema.json
open(os.path.join(base_dir,"config","settings_schema.json"),"w").write(json.dumps([{
  "name":"Theme settings",
  "settings":[
    {"type":"color","id":"background","label":"Background","default":"#ffffff"},
    {"type":"color","id":"text_color","label":"Text color","default":"#111111"},
    {"type":"color","id":"accent","label":"Accent color","default":"#1A73E8"},
    {"type":"checkbox","id":"enable_rtl","label":"Enable RTL (Arabic)","default": False}
  ]
}], indent=2))

# locales
open(os.path.join(base_dir,"locales","en.default.json"),"w").write(json.dumps({
  "general":{"search":{"search":"Search"}},
  "sections":{"header":{"cart":"Cart"},"footer":{"about":"About","links":"Links","newsletter":"Newsletter","email":"Your email","subscribe":"Subscribe"},"featured_collection":{"empty":"Add a collection to show products here."}},
  "products":{"product":{"add_to_cart":"Add to cart","sold_out":"Sold out"}},
  "collections":{"general":{"no_matches":"No products found"}}
}, indent=2))

# README
open(os.path.join(base_dir,"README.md"),"w").write("""
# Lumo Nest Starter Theme (Shopify OS 2.0)
Minimal gadgets-focused starter theme.

## Quick Start
npm i -g @shopify/cli@latest
shopify login --store your-dev-store.myshopify.com
shopify theme dev

## Push to dev store
shopify theme push --unpublished

## Customize
- Add menus (main, footer)
- Set Hero Banner & Featured collection in the editor
- Toggle RTL for Arabic in Theme settings
""".strip())

# Zip
zip_path = "/mnt/data/lumo-nest-starter-theme.zip"
with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as z:
    for root, _, files in os.walk(base_dir):
        for f in files:
            full = os.path.join(root, f)
            rel = os.path.relpath(full, base_dir)
            z.write(full, arcname=f"lumo-nest-starter-theme/{rel}")
            shopify theme push --unpublished
