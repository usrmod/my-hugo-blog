
---
title: "{{ replace .File.ContentBaseName `-` ` ` | title }}"  # Hugo archetype templating
date: "{{ .Date }}"
draft: true
tags: []
categories: []
showToc: true
TocOpen: false
hidemeta: false
comments: false
description: ""          # Short description shown under the title on the post page
canonicalURL: ""         # Example: "https://canonical.url/to/page"
disableShare: false
hideSummary: false      # true = hide the summary/excerpt on list pages
searchHidden: false     # true = hide from search index (if you enabled search)
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
UseHugoToc: true
cover:
  image: ""              # e.g. "cover.png" (with relative:true) or "/img/cover.png" or "https://..."
  alt: ""                # alt text for accessibility
  caption: ""            # caption under the cover image
  relative: true         # true for page bundles; false for absolute/URL paths
  hidden: false          # true = hide cover only on single post page
editPost:
  URL: ""                # e.g. "https://github.com/<user>/<repo>/blob/main/content"
  Text: "Suggest Changes"
  appendFilePath: true
---
