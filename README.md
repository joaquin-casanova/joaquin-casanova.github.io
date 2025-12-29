## Personal Blog Powered by Hugo & PaperMod

![Continuous Integration and Delivery](https://github.com/joaquin-casanova/joaquin-casanova.github.io/actions/workflows/gh-pages.yml/badge.svg)

### Pre-requisites

- [Hugo Extended](https://gohugo.io/installation/) v0.146.0 or greater
- Git

Verify Hugo installation:
```bash
hugo version
```

### Quick Start

1. **Clone the repository with submodules:**
```bash
git clone --recurse-submodules https://github.com/joaquin-casanova/joaquin-casanova.github.io.git
cd joaquin-casanova.github.io
```

2. **If you already cloned without submodules, initialize the theme:**
```bash
git submodule update --init --recursive
```

3. **Run local server:**
```bash
hugo server
```

The site will be available at http://localhost:1313/

### Common Commands

| Command | Description |
|---------|-------------|
| `hugo server` | Run local development server |
| `hugo server -D` | Run server including draft posts |
| `hugo --minify` | Build for production |
| `hugo new posts/my-post.md` | Create new blog post |
| `git submodule update --remote --merge` | Update theme to latest version |

### Project Structure

```
├── content/          # Blog posts and pages
│   ├── posts/        # Blog posts
│   ├── about.md      # About page
│   └── archives.md   # Archives page
├── layouts/          # Custom template overrides
├── themes/PaperMod/  # Theme (git submodule)
└── config.yml        # Hugo configuration
```

### Deployment

Automatic deployment via GitHub Actions on push to `main` branch.
