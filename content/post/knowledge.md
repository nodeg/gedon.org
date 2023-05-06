---
title: "Setting up a knowledge base"
date: 2023-05-06T21:29:28+02:00
draft: false
---

The idea of [migrating my website](/post/gh_pages/) from a custom workflow to GitHub Pages was born after
setting up a knowledge base I always wanted to be able to collect my knowledge in a central place.
Since I really like Hugo, I had a look if I can find a good theme that works well as a knowledge base. I came across [Geekdoc](https://github.com/thegeeklab/hugo-geekdoc) that looked really promising. I created a [fork](https://github.com/nodeg/prv-geekdoc.git) of it to be able to make some own adjustments and added it as a git submodule to my
main [GitHub repository](https://github.com/nodeg/knowledge) for the knowledge base. This theme is heavily relying on
NodeJS, so I had to add the following to my Hugo GitHub Action [workflow](https://github.com/nodeg/knowledge/blob/main/.github/workflows/hugo.yaml#L53-L57) for publishing it.

```yaml
       - name: Build theme
        run: |
          cd themes/hugo-geekdoc
          npm install
          npm run build
```
