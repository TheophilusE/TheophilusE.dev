---
title: "Personal Website"
date: 2022-10-01
tags: ["Project", "Hugo", "Static Site", "Portfolio"]
author: ["Theophilus Eriata"]
description: "A fast, SEO-friendly personal website built with Hugo and the PaperMod theme to showcase projects, blog posts, talks, and CV."
summary: "I developed a modular, content-driven personal website using Hugo. It features dynamic content organization, continuous deployment on Netlify, and a custom PaperMod theme for a clean, responsive design."
editPost:
  URL: "https://github.com/TheophilusE/TheophilusE.dev"
  Text: "GitHub Repository"
showToc: true
disableAnchoredHeadings: false
---

## Introduction

I wanted a single hub to present my projects, blog posts, talks, and CV in a cohesive, high-performance site. Instead of hand-coding each page or relying on a heavyweight CMS, I chose Hugo, a static site generator known for its speed and flexible content model. Over the course of six weeks, I built, iterated, and deployed my personal website at https://theophiluse.xiitechnologies.com.

## Motivation and Goals

I set out with three main objectives:

- Deliver near-instant page loads for visitors worldwide.
- Maintain a modular content structure so I can add new projects or posts without touching templates.
- Automate build and deploy steps for every content change via GitHub and Netlify.

Balancing performance, ease of maintenance, and scalability guided every decision.

## Tech Stack

| Component             | Technology         | Purpose                                      |
|-----------------------|--------------------|----------------------------------------------|
| Static Site Generator | Hugo (v0.94.2)     | Markdown → HTML, content organization        |
| Theme                 | PaperMod           | Clean, responsive UI with dark/light modes   |
| Styling               | Custom CSS + SCSS  | Overrides for typography, layout tweaks      |
| Hosting & CI/CD       | Netlify            | Automated builds, previews, global CDN       |
| Version Control       | GitHub             | Source code, content, issues, pull requests  |
| Analytics             | GoatCounter        | Privacy-focused visitor tracking             |

## Project Structure

Hugo’s directory layout keeps content, templates, and assets neatly separated:

- **archetypes/**
  Default front matter templates for new posts, projects, talks, etc.

- **content/**
  - **blog/**: Markdown posts with YAML front matter
  - **projects/**: Detailed project pages with galleries and links
  - **talks/**: Slides, abstracts, and embedded recordings
  - **teaching/**: Course notes and materials

- **layouts/**
  Custom templates overriding PaperMod where needed (footer, header).

- **static/**
  Favicon, images, PDFs (CV, papers).

- **assets/**
  SCSS partials and custom CSS.

- **config.yml**
  Global site config: menus, params, theme settings.

## Key Features

1. Responsive, Mobile-First Design:
   Every page adapts fluidly to phone, tablet, and desktop viewport sizes.

2. Modular Content with Taxonomies:
   I categorize posts by tags like “Hugo”, “JavaScript”, “Teaching”, and “FTC Robotics.” Site search and tag pages are auto-generated.

3. Dark/Light Mode Toggle:
   Visitors can switch themes; system preference is detected on first visit.

4. Instant Search:
   An Algolia-style search powered by Lunr.js for client-side indexing of titles and summaries.

5. Continuous Deployment:
   A push to any branch in GitHub triggers Netlify to build, test, and deploy (previews on pull requests included).

## Implementation Highlights

### Customizing PaperMod

I forked the PaperMod theme and:

- Adjusted the header to include quick links to my CV and GitHub.
- Overrode the footer to display current year and site license.
- Added a “Featured Project” section on the homepage via a custom partial.

### Asset Pipeline

Using Hugo Pipes, I compile SCSS into minified CSS, auto-generate image thumbnails, and fingerprint assets for long-term caching.

```yaml
# config.yml snippet
resources:
  scss:
    - src: "assets/scss/main.scss"
      output: "css/styles.min.css"
```

### Optimizations

- Enabled HTTP/2 push for CSS/JS bundles.
- Inline critical-path CSS for above-the-fold content.
- Lazy load below-the-fold images via `loading="lazy"`.

## Challenges & Solutions

- **Challenge:** Managing PDF downloads (CV, papers) without cluttering the repo.
  * **Solution:** Keep PDFs in `static/files/`, reference via shortcodes in Markdown.

- **Challenge:** Ensuring code snippet styling matched the theme.
  * **Solution:** Integrated Prism.js with custom PaperMod styles for consistency.

- **Challenge:** Balancing YAML front-matter verbosity with maintainability.
  * **Solution:** Created archetypes for each content type, pre-populating common fields.

## Results & Metrics

- Lighthouse Performance Score: **99/100** on desktop, **95/100** on mobile.
- First Contentful Paint under **0.8s** on average.
- Netlify build times: **<20 seconds** for full site.
- CI/CD uptime: **100%** (no failed deployments in last 6 months).

## Lessons Learned

- Static site generators shine when content updates are frequent but structure is consistent.
- Automating asset pipelines within Hugo significantly reduces dependencies.
- Abstracting common front-matter into archetypes saves hours for every new post or project.

## Future Improvements

- Integrate a headless CMS (e.g., Netlify CMS) for non-tech contributors to add content.
- Add multilingual support for langue française translations of key posts.
- Explore WebP image generation for even better performance.

## Related Links

- Hugo Documentation: https://gohugo.io/documentation/
- PaperMod Theme: https://github.com/adityatelange/hugo-PaperMod
- Netlify: https://www.netlify.com
