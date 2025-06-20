---

title: "Adding Giscus Comments to Hugo with PaperMod"
date: 2025-06-20T12:00:00Z
description: "Step-by-step guide to adding Giscus (GitHub-powered comments) to a Hugo blog using the PaperMod theme."
tags: \["hugo", "giscus", "comments", "paperMod", "blog"]
categories: \["guides"]
draft: false
image: "/images/giscus-hugo-banner.png"
---------------------------------------


![Giscus + Hugo](/images/giscus-hugo-banner.png)

If you're using [Hugo](https://gohugo.io/) and the [PaperMod theme](https://github.com/adityatelange/hugo-PaperMod), adding lightweight, privacy-respecting comments via [Giscus](https://giscus.app/) is a great way to engage readers. This guide walks through exactly how I integrated Giscus into my Hugo blog, including code examples and configuration tips.

## ✅ Why Giscus?

* **GitHub-powered**: Stores comments as GitHub Discussions
* **Spam-free**: Only GitHub users can comment
* **Lightweight**: No trackers, no ads
* **Free**: Hosted by GitHub

---

## 🔧 Step 1: Enable Discussions on Your GitHub Repo

Go to your GitHub repo (e.g., `yourusername/blog-comments`) and:

* Go to **Settings** → **Features**
* Check ✅ "Discussions"

---

## 🔧 Step 2: Configure Giscus on [giscus.app](https://giscus.app/)

Fill out the config form:

* Repository: `yourusername/yourrepo`
* Category: Create one like "General"
* Mapping: `pathname`
* Theme: `light`, `dark`, or `preferred_color_scheme`

Copy the generated HTML `<script>`.

Example:

```html
<script src="https://giscus.app/client.js"
        data-repo="yourusername/yourrepo"
        data-repo-id="REPO_ID"
        data-category="General"
        data-category-id="CATEGORY_ID"
        data-mapping="pathname"
        data-theme="preferred_color_scheme"
        crossorigin="anonymous"
        async>
</script>
```

---

## 🔧 Step 3: Add Giscus to Your Hugo Template

Instead of editing the theme directly, create an override in your project:

```bash
mkdir -p layouts/partials
```

Create a file `layouts/partials/comments.html` with your Giscus embed code.

Then, open (or override):

```bash
layouts/_default/single.html
```

And insert this before `{{ end }}`:

```go-html-template
{{ partial "comments.html" . }}
```

---

## 🧪 Step 4: Test in Local Dev

Run your server:

```bash
hugo server --bind=0.0.0.0 --baseURL=http://localhost:1313
```

Visit a post and scroll to the bottom. You should see Giscus loading.

---

## 🌐 Live Example

Check out Giscus working on my [Hello World post](https://chrispy.me/posts/hello-world/).

---

## 📌 Notes

* Giscus **requires users to be signed into GitHub** to comment.
* You can switch the `data-theme` dynamically for dark/light mode support.
* If it doesn't load: double-check your repo ID, category ID, and baseURL.

---

I hope this guide helps you set up a clean, modern comment system for your Hugo blog! 🚀

See you in the next post ✌️

-Chrispy