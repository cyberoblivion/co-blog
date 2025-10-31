
## Project Overview

CyberOblivion is a tech blog built with Jekyll 4.3.3, serving content at [blog.cyberoblivion.com](https://blog.cyberoblivion.com). The site uses the Minima theme with custom modifications and supports Vue.js components embedded within posts for interactive elements.

## Development Commands

### Local Development
```bash
bundle install                      # Install dependencies
bundle exec jekyll serve --port 4001  # Run development server (http://localhost:4001)
bundle exec jekyll serve --verbose  # Run with verbose output for troubleshooting
```

**Important**: Changes to `_config.yml` require a server restart. Port 4001 is specified in the config file.

## Architecture & Key Conventions

### Content Structure
- **Posts**: Located in `_posts/` with naming format `YYYY-MM-DD-title.markdown`
- **Categories**: Use lowercase, hyphenated names (e.g., `howto`, `review`, `tutorial`)
- **Permalinks**: Custom permalinks defined in post front matter (e.g., `/howto/add-vuejs-to-jekyll/`)
- **Front matter template**:
  ```yaml
  ---
  layout: post
  title: "Your Title"
  date: YYYY-MM-DD HH:MM:SS +0000
  categories: category-name
  permalink: /path/to/post/
  author: "Author Name"
  description: "SEO description"
  ---
  ```

### Theme & Styling System
- Base theme: Minima (~> 2.5)
- Custom styling: All custom styles go in `_sass/minima/co-mixins.scss` (extends Minima theme)
- The site overrides theme defaults through `_layouts/` and `_includes/` directories
- Default layout (`_layouts/default.html`) includes custom footer with CyberOblivion icon

### Dynamic Component Integration

**Vue.js Components**:
- Vue.js 3.2.45 is used for interactive components in posts
- Include Vue via CDN in individual posts or use the reusable `_includes/vuejs-example.html`
- Example integration pattern (from [_posts/2024-04-28-add-vuejs-to-jekyll.markdown](/_posts/2024-04-28-add-vuejs-to-jekyll.markdown)):
  ```html
  <script src="https://cdnjs.cloudflare.com/ajax/libs/vue/3.2.45/vue.global.prod.min.js"></script>
  {% raw %}
  <div id="app">
    <!-- Vue content here -->
  </div>
  {% endraw %}
  ```
- When embedding Vue components, wrap in `{% raw %}{% endraw %}` to prevent Liquid template conflicts

**Bootstrap Support**:
- Bootstrap 5.3.3 can be conditionally loaded per-post using front matter flag:
  ```yaml
  bootstrap-enabled: true
  ```
- Bootstrap JS is loaded via [_layouts/default.html](/_layouts/default.html#L23-L27) when enabled

### Reusable Components (`_includes/`)
- `vuejs-example.html`: Pre-built Vue.js markdown editor component
- `google-analytics.html`: GA4 tracking (ID configured in `_config.yml`)
- `comments.html`: Comment system integration
- `header.html`, `head.html`, `copywrite.html`: Layout components

### Configuration (`_config.yml`)
Key settings:
- `port: 4001` - Development server port
- `google_analytics` - GA4 tracking ID
- `baseurl: ""` - Empty for root domain deployment
- `url: "https://blog.cyberoblivion.com"` - Production URL
- Custom fields: `author`, `copywrite`, `start_year`, `youtube_username`

### Asset Organization
- Post-specific assets: `assets/` directory (organize by topic/post)
- Site icon: `assets/co-icon.svg` (used in footer)
- Internal links: Always use `{{ site.baseurl }}` prefix for portability

## Plugins
- `jekyll-feed` (~> 0.12) - RSS feed generation
- `jekyll-sitemap` - Automatic sitemap.xml generation

## Deployment
- Auto-deploys from `master` branch
- Ensure all internal links use Liquid tags (`{{ site.baseurl }}`, `{{ site.url }}`) for correct rendering
- Verify `CNAME` file is present for custom domain
