
---
# =========================================
# PaperMod post archetype (YAML front matter)
# File path suggestion:
#   archetypes/posts.md   (recommended if your section is content/posts/)
#
# Usage examples:
#   hugo new content posts/my-first-post.md
#   hugo new content posts/my-first-post/index.md   # leaf bundle (best for per-post images)
# =========================================

# Title is generated from the file/folder name.
# Example: "my-first-post" -> "My First Post"
title: "My First Post"  # Hugo archetype templating

# Hugo injects the current time when running `hugo new ...`.
# Keep it quoted so YAML parses it as a string even with timezone.
date: "2026-02-10T00:27:04+05:30"

# New posts should usually start as drafts.
draft: true

# Taxonomy (enable in your hugo.yml: taxonomies: { tag: tags, category: categories } if you want)
tags: ["tag1","tag2"]
categories: ["category1", "caterogy2"]

# Optional: author shown in post meta (string or list) if your PaperMod config displays it.
# author: "Me"
# author: ["Me", "You"]  # multiple authors

# -----------------------------------------
# Table of contents (PaperMod page variables)
# PaperMod requires exact casing: showToc, TocOpen
# -----------------------------------------
showToc: true
TocOpen: false

# Meta + UX toggles
hidemeta: false
comments: false
description: "description testing not working"          # Short description shown under the title on the post page on full page
summary: "Getting started with summary"                 # summary shown when post is closed
canonicalURL: ""         # Example: "https://canonical.url/to/page"

# Share icons
disableShare: false

# -----------------------------------------
# Syntax highlighting choice
# Prefer setting this globally in hugo.yml:
#   params:
#     assets:
#       disableHLJS: true
# Then configure Chroma via:
#   markup:
#     highlight: { codeFences: true, ... }
# If you really want per-post override, uncomment the line below (use only ONE disableHLJS line).
# -----------------------------------------
# disableHLJS: true   # Disable Highlight.js loading for this post (use Chroma instead)

# List page behavior
hideSummary: false      # true = hide the summary/excerpt on list pages

# Search-related
searchHidden: false     # true = hide from search index (if you enabled search)

# Post meta display toggles
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true

# Use Hugo's built-in TOC instead of theme's TOC rendering (optional)
UseHugoToc: true

# summary: "getting started"

# -----------------------------------------
# Cover image (works great with page bundles)
# If you use a leaf bundle (posts/slug/index.md) and put image next to it,
# set `relative: true` and use `image: "cover.png"`.
# -----------------------------------------
cover:
  image: ""              # e.g. "cover.png" (with relative:true) or "/img/cover.png" or "https://..."
  alt: ""                # alt text for accessibility
  caption: ""            # caption under the cover image
  relative: true         # true for page bundles; false for absolute/URL paths
  hidden: false          # true = hide cover only on single post page

# -----------------------------------------
# "Edit this page" link (optional)
# -----------------------------------------
editPost:
  URL: ""                # e.g. "https://github.com/<user>/<repo>/blob/main/content"
  Text: "Suggest Changes"
  appendFilePath: true
---
<!-- more -->
> TLDR; 
---

Testing