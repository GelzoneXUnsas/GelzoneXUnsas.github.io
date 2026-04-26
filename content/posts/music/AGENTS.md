# PROJECT KNOWLEDGE BASE

**Generated:** 2026-04-26 12:44:05
**Commit:** 70017fc
**Branch:** main

## OVERVIEW
Music post archive with 15 posts covering anime/game soundtrack fingerstyle guitar covers and arrangements.

## STRUCTURE
```
content/posts/music/
├── *.md                    # 15 posts, mixed Japanese/English filenames
```

## WHERE TO LOOK

| Task | Location | Notes |
|------|----------|-------|
| Add new music post | content/posts/music/ | Create new .md file |
| Edit existing post | content/posts/music/{filename}.md | Direct edit |
| Find post by anime | grep "anime name" content/posts/music/ | Search content |

## CONVENTIONS

Filenames:
- Mixed scripts allowed: English titles, Japanese titles, or combined
- Special characters allowed: spaces, punctuation, Japanese kanji/kana
- Examples: `Your Name..md`, `Ref: rain.md`, `どんな星空よりも、どんな思い出よりも.md`

Frontmatter required fields:
```yaml
---
title: "Post Title"
date: YYYY-MM-DDTHH:MM:SS-08:00
draft: false
author: "Gelzone Unsas"
tags: ["Music"]
featured_image: 'https://...'
description: "brief description"
---
```

## ANTI-PATTERNS

DO NOT use Japanese date format in frontmatter. Always use ISO 8601 with Pacific timezone offset (`-08:00` for PST, `-07:00` for PDT).

DO NOT skip the `draft: false` line if post is ready to publish.

DO NOT use relative YouTube links. Use full `https://www.youtube.com/watch?v=...` format in both frontmatter and youtube shortcode.