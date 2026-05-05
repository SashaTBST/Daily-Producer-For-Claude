# 11ty Starter Config Baseline
Eleventy v3.1 | Last verified: 2026-04-27

⚠ v4 warning: this skill is pinned to v3.1. If your project runs v4+, verify these configs
against the v4 changelog before using — some options may have changed.

---

## How to use this file
Plain English: this file gives your developer the standard starting point for every new 11ty project.
It covers folder structure, config, and security headers. Copy it to your project, fill in your
site details, and you have a working baseline in minutes rather than hours.

Steps:
1. Tell your developer: "set up 11ty using baseline/starter-config.md"
2. They create the folder structure and copy the config files
3. Run `npm install` then `npm start` to see the site locally
4. Adjust the site name, URL, and author fields in `_data/site.json`

---

## Recommended Folder Structure

```
your-project/
├── src/                    ← all your source content goes here
│   ├── _includes/          ← reusable template parts (header, footer, nav)
│   │   ├── layouts/        ← full page layouts
│   │   │   └── base.njk    ← base layout all pages extend from
│   │   ├── header.njk      ← site header partial
│   │   └── footer.njk      ← site footer partial
│   ├── _data/              ← site-wide data files (JSON/JS)
│   │   └── site.json       ← site name, URL, author — used across all templates
│   ├── assets/             ← CSS, JS, images (copied to output as-is)
│   │   ├── css/
│   │   ├── js/
│   │   └── images/
│   ├── posts/              ← blog posts (if needed) — each is a .md file
│   └── index.njk           ← homepage
├── _site/                  ← built output (never edit — auto-generated)
├── .eleventy.js            ← main 11ty config file
├── package.json
├── .gitignore
└── _headers                ← Netlify security headers (or vercel.json for Vercel)
```

---

## .eleventy.js — Main Config File

```js
// .eleventy.js
// This is the brain of your 11ty project.
// It tells 11ty where your source files are, where to put the built output,
// and which files to copy across without processing.

module.exports = function(eleventyConfig) {

  // PASSTHROUGH COPIES
  // These folders are copied directly to the output without any processing.
  // Add any folder containing files that should be served as-is.
  eleventyConfig.addPassthroughCopy("src/assets");
  eleventyConfig.addPassthroughCopy({ "src/assets/images": "images" });

  // PLUGINS
  // Uncomment the plugins you need. Install each via npm first.

  // Image optimisation — converts images to WebP, generates responsive sizes
  // npm install @11ty/eleventy-img
  // const Image = require("@11ty/eleventy-img");

  // Hierarchical navigation menus
  // npm install @11ty/eleventy-navigation
  // const navigationPlugin = require("@11ty/eleventy-navigation");
  // eleventyConfig.addPlugin(navigationPlugin);

  // RSS feed — good SEO practice even if not actively used
  // npm install @11ty/eleventy-plugin-rss
  // const rssPlugin = require("@11ty/eleventy-plugin-rss");
  // eleventyConfig.addPlugin(rssPlugin);

  // COLLECTIONS
  // Collections group content together (e.g. all blog posts).
  // 11ty auto-creates a "posts" collection if you use the posts/ folder.
  // Add custom collections here if needed:
  // eleventyConfig.addCollection("caseStudies", (collectionApi) =>
  //   collectionApi.getFilteredByGlob("src/case-studies/*.md")
  // );

  // FILTERS
  // Filters transform data in templates (e.g. format a date).
  eleventyConfig.addFilter("readableDate", (dateObj) => {
    // Converts a date object to a human-readable string: "27 April 2026"
    return new Intl.DateTimeFormat("en-AU", {
      day: "numeric",
      month: "long",
      year: "numeric",
    }).format(dateObj);
  });

  // DIRECTORY CONFIG
  // Tells 11ty where to find source files and where to put the built output.
  return {
    dir: {
      input: "src",       // your source files
      output: "_site",    // built output (deploy this folder)
      includes: "_includes",
      data: "_data",
    },
    // Template formats 11ty will process
    templateFormats: ["njk", "md", "html"],
    // Use Nunjucks as the default template language
    markdownTemplateEngine: "njk",
    htmlTemplateEngine: "njk",
  };
};
```

---

## package.json Baseline

```json
{
  "name": "your-site-name",
  "version": "1.0.0",
  "description": "Your site description",
  "scripts": {
    "start": "npx @11ty/eleventy --serve",
    "build": "npx @11ty/eleventy",
    "debug": "DEBUG=Eleventy* npx @11ty/eleventy"
  },
  "dependencies": {
    "@11ty/eleventy": "^3.1.0",
    "@11ty/eleventy-img": "^6.0.0",
    "@11ty/eleventy-navigation": "^1.0.0",
    "@11ty/eleventy-plugin-rss": "^2.0.0"
  },
  "devDependencies": {}
}
```

Note: versions are pinned to major.minor — update deliberately, not automatically.
Run `npm audit` before every deploy to catch known vulnerabilities.

---

## _data/site.json — Site-Wide Data

```json
{
  "name": "Your Site Name",
  "url": "https://yoursite.com",
  "author": "Your Name",
  "description": "One sentence about what this site is.",
  "language": "en-AU"
}
```

Access in any template: `{{ site.name }}`, `{{ site.url }}`, etc.

---

## Base Layout — src/_includes/layouts/base.njk

```html
<!DOCTYPE html>
<!--
  base.njk — every page extends this layout.
  To use it: add `layout: layouts/base.njk` to a page's front matter.
-->
<html lang="{{ site.language }}">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- Page title — falls back to site name if no page title set -->
  <title>{% if title %}{{ title }} — {% endif %}{{ site.name }}</title>

  <!-- SEO description — use page description if set, otherwise site description -->
  <meta name="description" content="{{ description or site.description }}">

  <!-- Canonical URL — important for SEO, prevents duplicate content issues -->
  <link rel="canonical" href="{{ site.url }}{{ page.url }}">

  <!-- Your stylesheet -->
  <link rel="stylesheet" href="/assets/css/main.css">
</head>
<body>
  {% include "header.njk" %}

  <!-- Main content — each page's content is injected here -->
  <main>
    {{ content | safe }}
  </main>

  {% include "footer.njk" %}

  <!-- Scripts go at the bottom so they don't block page rendering -->
  <!-- Add SRI integrity attribute to any script loaded from a CDN -->
</body>
</html>
```

---

## Security Headers — Choose One Based on Deployment

### Netlify — `_headers` file (place at project root, not in src/)

```
# _headers — Netlify reads this file and adds these headers to every response.
# Copy this to your project root. Adjust CSP directives for your specific scripts.

/*
  # Prevents the browser from guessing the file type (security)
  X-Content-Type-Options: nosniff

  # Prevents your site from being embedded in iframes on other sites (clickjacking)
  X-Frame-Options: DENY

  # Controls how much referrer info is sent when users click links
  Referrer-Policy: strict-origin-when-cross-origin

  # Forces HTTPS for 1 year — only enable once site is fully on HTTPS
  Strict-Transport-Security: max-age=31536000; includeSubDomains

  # Content Security Policy — controls what scripts/styles/fonts can load
  # Start with report-only to catch issues before enforcing
  # Content-Security-Policy-Report-Only: default-src 'self'; script-src 'self'; style-src 'self'
  # Once confirmed clean, switch to enforcing:
  Content-Security-Policy: default-src 'self'; script-src 'self' https://www.googletagmanager.com; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self'; connect-src 'self'
```

### Vercel — `vercel.json` (place at project root)

```json
{
  "buildCommand": "npx @11ty/eleventy",
  "outputDirectory": "_site",
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        { "key": "X-Content-Type-Options", "value": "nosniff" },
        { "key": "X-Frame-Options", "value": "DENY" },
        { "key": "Referrer-Policy", "value": "strict-origin-when-cross-origin" },
        { "key": "Strict-Transport-Security", "value": "max-age=31536000; includeSubDomains" },
        {
          "key": "Content-Security-Policy",
          "value": "default-src 'self'; script-src 'self' https://www.googletagmanager.com; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self'; connect-src 'self'"
        }
      ]
    }
  ]
}
```

### S3 + CloudFront
Security headers are set via a CloudFront Response Headers Policy.
Create the policy in AWS Console → CloudFront → Policies → Response headers.
Add the same headers as above. Attach the policy to your CloudFront distribution's behaviour.

---

## .gitignore

```
# Built output — never commit this, it's regenerated every build
_site/

# Node modules — always reinstall from package.json
node_modules/

# Environment variables — NEVER commit this file
.env
.env.local
.env.*.local

# OS files
.DS_Store
Thumbs.db

# Editor files
.vscode/
.idea/
```

---

## Security checklist before first deploy
- [ ] `.env` is in `.gitignore` and not committed
- [ ] No API keys in any template, `_data/` file, or built `_site/` output
- [ ] Security headers file present (`_headers` or `vercel.json` or CloudFront policy)
- [ ] CSP deployed as report-only first, then enforced after no violations
- [ ] SRI integrity attributes on all CDN-hosted scripts
- [ ] `npm audit` run and clean
- [ ] All pages have unique `<title>` and `<meta name="description">`
- [ ] Canonical URLs set on every page
