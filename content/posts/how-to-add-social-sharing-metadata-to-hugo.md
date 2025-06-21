+++
title: "How to Add Social Sharing Metadata to Hugo"
date: 2025-06-20
description: "Make your Hugo blog share-worthy on social platforms by implementing Open Graph and Twitter Card metadata." 
tags: ["hugo", "seo", "metadata", "social-sharing", "opengraph"] 
categories: ["guides"] 
draft: false 
image: "/images/hugo-social-metadata-banner.png"
+++

![Add Social Sharing Metadata](/images/hugo-social-metadata-banner.png)


When you share your Hugo blog on social media, wouldn't it be great if it showed a proper image, title, and description preview? This guide will walk you through implementing **Open Graph** and **Twitter Card** metadata in your Hugo project.

## 🧠 Why Social Metadata Matters

- It improves how your links appear on Twitter, LinkedIn, Discord, etc.
- Boosts click-through rates and user engagement
- Helps establish consistent branding and visibility

---

## 🧩 Step 1: Add Metadata in `head.html`

Create (or edit) this partial:

```bash
layouts/partials/head.html
```

Add the following:

```html
<!-- Open Graph -->
<meta property="og:title" content="{{ .Title }}" />
<meta property="og:description" content="{{ .Params.description }}" />
<meta property="og:type" content="article" />
<meta property="og:url" content="{{ .Permalink }}" />
<meta property="og:image" content="{{ with .Params.image }}{{ . | absURL }}{{ else }}https://chrispy.me/images/avatar.png{{ end }}" />

<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="{{ .Title }}" />
<meta name="twitter:description" content="{{ .Params.description }}" />
<meta name="twitter:image" content="{{ with .Params.image }}{{ . | absURL }}{{ else }}https://chrispy.me/images/avatar.png{{ end }}" />
```

---

## 🧾 Step 2: Add Metadata to Front Matter

Inside your post `.md` files:

```toml
+++
title = "Your Post Title"
description = "A short description for social cards."
image = "/images/custom-thumbnail.png"
+++
```

---

## 🧪 Step 3: Test Your Pages

- [Twitter Card Validator](https://cards-dev.twitter.com/validator)
- [Open Graph Tester](https://www.opengraph.xyz/)

Make sure:

- Titles and descriptions match your post
- Your featured image is displayed

---

## 🛡️ Step 4: Add Fallback Metadata (Optional but Recommended)

To ensure metadata always renders properly, add site-wide fallback values in your `config.toml`:

```toml
[params]
  description = "Welcome to my blog — exploring Hugo, development, and digital craftsmanship."
  image = "/images/avatar.png"
```

Then update your `head.html` partial to fallback to these site-wide values:

```html
<meta property="og:description" content="{{ with .Params.description }}{{ . }}{{ else }}{{ .Site.Params.description }}{{ end }}" />
<meta name="twitter:description" content="{{ with .Params.description }}{{ . }}{{ else }}{{ .Site.Params.description }}{{ end }}" />

<meta property="og:image" content="{{ with .Params.image }}{{ . | absURL }}{{ else }}{{ .Site.Params.image | absURL }}{{ end }}" />
<meta name="twitter:image" content="{{ with .Params.image }}{{ . | absURL }}{{ else }}{{ .Site.Params.image | absURL }}{{ end }}" />
```

---

## ✅ Bonus Tips

- Store your images in `static/images/`
- Use `absURL` to ensure full URLs are generated
- Add default `.Site.Params.description` if you want global fallbacks

---

With just a few tags and some thoughtful front matter, you can make your Hugo site look polished and share-ready across the web. 🎉

See you in the next post ✌️

-Chrispy