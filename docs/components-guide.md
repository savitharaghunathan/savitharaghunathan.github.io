# Components Guide

This guide shows you how to use the reusable components and add new sections to your website.

## Quick Start: Adding a New Section

### Method 1: Using the Template Include

```liquid
{% raw %}{% include section-template.html 
  title="My New Section"
  intro="Description of what this section contains"
  category="Optional Category"
  grid_size="small" %}{% endraw %}
```

### Method 2: Manual Structure

```html
<section class="content-section">
  <h2 class="section-title">Section Title</h2>
  <div class="section-intro">
    <p>Section description goes here...</p>
  </div>
  <div class="content-grid">
    <article class="content-card">
      <div class="card-content">
        <h3 class="card-title">
          <a href="link-url" target="_blank">Item Title</a>
        </h3>
        <div class="card-meta">
          <span class="tag">Category</span>
          <span class="tag">Date</span>
        </div>
        <div class="card-description">
          <p>Item description...</p>
        </div>
        <a href="link-url" class="action-button" target="_blank">
          <span>🔗</span>
          View Item
        </a>
      </div>
    </article>
  </div>
</section>
```

## Available CSS Classes

### Grid Layouts
- `.content-grid` - Default grid (400px min-width)
- `.content-grid.small` - Small grid (300px min-width)
- `.content-grid.large` - Large grid (500px min-width)

### Cards
- `.content-card` - Main card container
- `.card-content` - Card content wrapper
- `.card-title` - Card title styling
- `.card-meta` - Metadata area (tags, dates)
- `.card-description` - Description text

### Tags
- `.tag` - Default tag styling
- `.tag.primary` - Primary colored tag

### Buttons
- `.action-button` - Primary button
- `.action-button.secondary` - Secondary button

### Sections
- `.content-section` - Section container
- `.section-title` - Section heading
- `.section-intro` - Section description
- `.section-category` - Category container
- `.category-title` - Category heading

### Lists
- `.content-list` - Unstyled list
- `.content-list li` - List items
- `.content-list a` - List links

## Examples

### Simple Section
```html
<section class="content-section">
  <h2 class="section-title">My Projects</h2>
  <div class="content-grid">
    <article class="content-card">
      <div class="card-content">
        <h3 class="card-title">
          <a href="https://github.com/example" target="_blank">Project Name</a>
        </h3>
        <div class="card-meta">
          <span class="tag">Open Source</span>
          <span class="tag">2024</span>
        </div>
        <div class="card-description">
          <p>Brief project description...</p>
        </div>
      </div>
    </article>
  </div>
</section>
```

### Section with Categories
```html
<section class="content-section">
  <h2 class="section-title">Technical Writing</h2>
  <div class="section-intro">
    <p>Articles and technical documentation I've written.</p>
  </div>
  <div class="section-category">
    <h3 class="category-title">Blog Posts</h3>
    <div class="content-grid">
      <!-- Blog post cards -->
    </div>
  </div>
  <div class="section-category">
    <h3 class="category-title">Documentation</h3>
    <div class="content-grid">
      <!-- Documentation cards -->
    </div>
  </div>
</section>
```

### List-based Section
```html
<section class="content-section">
  <h2 class="section-title">Resources</h2>
  <div class="content-grid">
    <article class="content-card">
      <div class="card-content">
        <h3 class="card-title">Useful Links</h3>
        <ul class="content-list">
          <li><a href="#" target="_blank">Link 1</a></li>
          <li><a href="#" target="_blank">Link 2</a></li>
          <li><a href="#" target="_blank">Link 3</a></li>
        </ul>
      </div>
    </article>
  </div>
</section>
```

## Tips

1. **Consistent Naming**: Use descriptive titles and consistent tag names
2. **External Links**: Always use `target="_blank"` for external links
3. **Responsive**: All components are mobile-responsive by default
4. **Icons**: Use emoji icons (🔗, 📁, 🎥, etc.) for visual appeal
5. **Grid Sizes**: Choose grid size based on content length and complexity

## File Structure

```
assets/css/
├── main.css          # Base styles
└── components.css    # Reusable components

_includes/
└── section-template.html  # Section template

docs/
└── components-guide.md    # This guide
``` 