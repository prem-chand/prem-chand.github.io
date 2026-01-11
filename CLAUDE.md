# Hugo Stack Personal Blog - Context

## Project Overview
Personal blog built with Hugo using the Stack theme for publishing technical articles and blog posts.

**Tech Stack:**
- Hugo static site generator
- Stack theme (https://github.com/CaiJimmy/hugo-theme-stack)
- Markdown for content
- GitHub Pages for hosting

## Project Structure
```
/
├── config.yaml              # Hugo configuration
├── content/
│   └── post/               # Blog posts directory
│       └── YYYY-MM-DD-title/
│           ├── index.md    # Post content
│           └── images/     # Post-specific images
├── static/
│   └── images/             # Global images
├── themes/
│   └── hugo-theme-stack/   # Stack theme (submodule)
├── public/                 # Generated site (git-ignored)
└── .github/
    └── workflows/          # GitHub Actions for deployment
```

## Common Commands

### Local Development
```bash
# Start development server with drafts
hugo server -D

# Start server without drafts
hugo server

# Build site for production
hugo

# Clean generated files
rm -rf public resources
```

### Content Management
```bash
# Create new blog post (Hugo will create the folder structure)
hugo new content/post/my-new-post/index.md

# Alternative: Create post with current date
hugo new content/post/$(date +%Y-%m-%d)-my-post/index.md
```

### Deployment
- Push to `main` branch triggers GitHub Actions workflow
- Workflow builds Hugo site and deploys to `gh-pages` branch
- Check deployment: https://github.com/[username]/[repo]/actions

## Writing Blog Posts

### Front Matter Template
Every post should have this front matter in `index.md`:

```yaml
---
title: "Your Post Title"
description: "Brief description for SEO and previews"
date: 2024-01-15T10:00:00+00:00
draft: false
slug: "post-url-slug"
image: "cover.jpg"  # Optional: cover image in same directory
categories:
  - Technology
  - Tutorial
tags:
  - Hugo
  - Web Development
  - Blogging
---
```

### Content Organization

**Post Directory Structure:**
```
content/post/my-awesome-post/
├── index.md          # The blog post content
├── cover.jpg         # Cover image (optional)
└── images/           # Post-specific images
    ├── diagram.png
    └── screenshot.jpg
```

**Image References in Posts:**
- Same directory: `![Description](image.png)`
- Images subfolder: `![Description](images/diagram.png)`
- Static folder: `![Description](/images/global-image.png)`

### Markdown Guidelines

**Code Blocks:**
```markdown
```python
def hello_world():
    print("Hello, World!")
```
```

**Callouts/Admonitions (Stack theme feature):**
```markdown
{{< notice note >}}
This is a note callout
{{< /notice >}}

{{< notice tip >}}
This is a tip callout
{{< /notice >}}

{{< notice warning >}}
This is a warning callout
{{< /notice >}}
```

**Math Equations (if enabled):**
- Inline: `$E = mc^2$`
- Block: `$$E = mc^2$$`

## Stack Theme Features

### Categories vs Tags
- **Categories:** Broad topics (max 2-3 per post)
  - Examples: Tutorial, Research, Personal, Technology
- **Tags:** Specific keywords (5-10 per post)
  - Examples: Python, Machine Learning, Hugo, Deployment

### Archives
- Posts automatically organized by date
- Accessible at `/archives/`

### Search
- Built-in search functionality
- Searches titles, descriptions, and content

## Content Guidelines

### Post Creation Workflow
1. Create new post: `hugo new content/post/post-title/index.md`
2. Set `draft: true` while writing
3. Add cover image (recommended: 1200x630px)
4. Write content in Markdown
5. Add appropriate categories and tags
6. Preview locally: `hugo server -D`
7. Set `draft: false` when ready
8. Commit and push to deploy

### Writing Style
- Use clear, descriptive titles
- Start with engaging introduction
- Break content into sections with headers
- Use code blocks for technical content
- Add images to break up text
- Include a conclusion/summary

### SEO Best Practices
- Write descriptive `description` field (150-160 chars)
- Use meaningful slugs (lowercase, hyphens)
- Optimize images before adding (<500KB)
- Use descriptive alt text for images
- Include relevant internal links

## Important Configuration

### config.yaml Key Settings
```yaml
baseURL: "https://[username].github.io/"
languageCode: "en-us"
title: "Your Blog Title"
theme: "hugo-theme-stack"

params:
  sidebar:
    emoji: "🎯"
  comments:
    enabled: true  # If using comments system
  
  # Image processing
  imageProcessing:
    cover:
      enabled: true
      relative: true
```

## Troubleshooting

### Common Issues
- **Images not showing:** Check file path and ensure image is in post directory
- **Post not appearing:** Verify `draft: false` and date is not in future
- **Build fails:** Check Hugo version compatibility with theme
- **Formatting issues:** Validate YAML front matter syntax

### Build Errors
```bash
# Check Hugo version
hugo version

# Validate config
hugo config

# Build with verbose output
hugo --verbose
```

## Git Workflow

### Before Committing
```bash
# Test build locally
hugo

# Check for broken links (if using link checker)
# Preview final site
hugo server

# Commit and push
git add .
git commit -m "Add new post: [Post Title]"
git push origin main
```

### Branch Strategy
- `main`: Source files
- `gh-pages`: Auto-generated by GitHub Actions (don't touch)

## Deployment Pipeline

GitHub Actions workflow (`.github/workflows/hugo.yml`) automatically:
1. Checks out repository
2. Sets up Hugo
3. Builds the site
4. Deploys to GitHub Pages

**Typical deployment time:** 1-3 minutes

## Quick Reference

### Post Front Matter Quick Copy
```yaml
---
title: ""
description: ""
date: 2024-01-15T10:00:00+00:00
draft: true
slug: ""
image: ""
categories:
  - 
tags:
  - 
---
```

### Common Hugo Template Functions
- `{{ .Title }}` - Post title
- `{{ .Date }}` - Post date
- `{{ .Content }}` - Post content
- `{{ .Summary }}` - Auto-generated summary

## Personal Preferences

### When Claude Helps with Posts
- Use clear, technical writing style
- Prefer code examples with explanations
- Include practical, actionable content
- Add visual breaks (images, code blocks, callouts)
- Optimize for both technical accuracy and readability

### Default Post Settings
- Categories: Choose from existing set for consistency
- Tags: Be specific but don't over-tag
- Images: Always optimize before adding
- Code blocks: Always specify language for syntax highlighting

## Resources
- Hugo Documentation: https://gohugo.io/documentation/
- Stack Theme Docs: https://stack.jimmycai.com/
- Markdown Guide: https://www.markdownguide.org/
- Hugo Shortcodes: https://gohugo.io/content-management/shortcodes/