---
title: "Migrating to GitHub Pages"
date: 2023-05-06T21:29:28+02:00
draft: false
---

Today I migrated this website from a manual workflow to GitHub Pages. I followed the
[docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site) for
adding a custom domain, which was to set proper `A`, `AAAA` and `CNAME` records for the domain I
want to use and enable GitHub pages for the repository. In the beginning GitHub had some issues
in finding the new entries and did not went past the following message:

![Image Custom domain 1](/post/gh_pages/c_domain1.png)

The DNS records, however, were properly set to the GitHub Pages server found in the
above documentation.

```bash
$ dog A AAAA gedon.dev
   A gedon.dev. 54m36s   185.199.108.153
   A gedon.dev. 54m36s   185.199.109.153
   A gedon.dev. 54m36s   185.199.110.153
   A gedon.dev. 54m36s   185.199.111.153
AAAA gedon.dev. 54m36s   2606:50c0:8000::153
AAAA gedon.dev. 54m36s   2606:50c0:8001::153
AAAA gedon.dev. 54m36s   2606:50c0:8003::153
AAAA gedon.dev. 54m36s   2606:50c0:8002::153

$ dog A www.gedon.dev
CNAME www.gedon.dev.   54m36s   "nodeg.github.io."
    A nodeg.github.io. 54m36s   185.199.110.153
    A nodeg.github.io. 54m36s   185.199.111.153
    A nodeg.github.io. 54m36s   185.199.109.153
    A nodeg.github.io. 54m36s   185.199.108.153
```

After a couple of retries by removing and adding the custom domain to the repository, it finally worked.
![Image Custom domain 1](/post/gh_pages/c_domain2.png)
For downloading and installing Hugo and then building the website every time a new commit
is added to the `main` branch, I set up the following GitHub Action [workflow](https://github.com/nodeg/gedon.org/blob/main/.github/workflows/hugo.yml):

```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.111.3
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass Embedded
        run: sudo snap install dart-sass-embedded
      - name: Checkout
        uses: actions/checkout@v3
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v3
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          # For maximum backward compatibility with Hugo modules
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```

The only downside so far is that GitHub does not allow to modify the HTTP headers, yet.
I hope this will be added in the future.
