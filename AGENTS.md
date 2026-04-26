# PROJECT KNOWLEDGE BASE

**Generated:** 2026-04-26 12:44:05
**Commit:** 70017fc
**Branch:** main

## OVERVIEW
Hugo static site for personal portfolio/blog (Lucas Li/Gelzone Unsas). Uses Ananke theme, outputs to `docs/` for GitHub Pages. Content: music, games, projects posts with Japanese content mixed.

## STRUCTURE
```
./
├── config.toml          # Hugo config (publishDir=docs)
├── content/            # Markdown posts (music, games, projects, tags)
├── layouts/             # Custom templates override theme
├── static/             # Static assets (images/games, music, profile)
├── themes/ananke/       # Theme submodule
├── archetypes/         # Content templates
├── data/                # Empty (deviation from standard)
├── docs/                # Built output (not public/)
└── resources/          # Auto-generated assets
```

## WHERE TO LOOK
| Task | Location | Notes |
|------|----------|-------|
| Edit site config | config.toml | BaseURL, theme, menu, socials |
| Add post | content/posts/{category}/ | Kebab-case filename |
| Custom layout | layouts/index.html | Overrides theme |
| Static assets | static/images/ | games, music, profile, others |
| About page | content/about/ | Single page section |

## ANTI-PATTERNS (THIS PROJECT)
- DO NOT clear Hugo cache if you want to save progress
- DO NOT commit the public/ directory (use docs/ instead)
- DO NOT edit files in themes/ananke directly (use layouts/ override)

## UNIQUE STYLES
- Japanese filenames mixed with English kebab-case
- `publishDir = "docs"` instead of standard `public/`
- Empty `data/` directory
- `hasCJKLanguage = true` in config
- Social links via Ananke theme params

## COMMANDS
```bash
hugo server          # Local dev
hugo                 # Build to docs/
hugo new posts/category/my-post.md  # Create post
```

## NOTES
- Theme is git submodule: `themes/ananke`
- Built output served from `docs/` (GitHub Pages)
- CJK language support enabled
- robots.txt enabled
- Google Analytics disabled (empty string)
