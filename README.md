# Personal Blog

A minimal GitHub Pages blog built with Jekyll.

Project path:

```text
/home/zelustek/nvme1n1p1/personal-blog
```

## Run locally

```bash
bundle install
bundle exec jekyll serve
```

Then open `http://localhost:4000`.

## Write a post

Create a Markdown file under `_posts`:

```text
YYYY-MM-DD-title.md
```

Use this front matter:

```markdown
---
title: "Post title"
date: 2026-05-10
categories: [ai]
tags: [notes]
---
```

## Publish

Create a GitHub repository named:

```text
your-github-username.github.io
```

Then push this folder to that repository:

```bash
git add .
git commit -m "Initial blog"
git remote add origin git@github.com:your-github-username/your-github-username.github.io.git
git push -u origin main
```

If you prefer HTTPS, use this remote instead:

```bash
git remote add origin https://github.com/your-github-username/your-github-username.github.io.git
```

In GitHub, open the repository settings:

1. Go to `Settings` -> `Pages`.
2. Under `Build and deployment`, choose `GitHub Actions`.
3. Wait for the `Deploy Jekyll site to Pages` workflow to finish.

Your blog will be available at:

```text
https://your-github-username.github.io
```
