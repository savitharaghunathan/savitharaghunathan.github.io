# Savitha Raghunathan's Personal Website

A modern, responsive personal website built with Jekyll and hosted on GitHub Pages. Features include a blog, podcast collection, and about page with year-wise organization and reverse chronological ordering.

## Features

- **Modern Design**: Clean, responsive design with smooth animations
- **Blog System**: Year-wise organized blog posts with categories and tags
- **Podcast Collection**: Audio player integration with episode details
- **About Page**: Personal information and contact details
- **SEO Optimized**: Built-in SEO tags and sitemap generation
- **RSS Feed**: Automatic RSS feed generation for blog posts
- **Mobile Responsive**: Optimized for all device sizes
- **GitHub Pages Ready**: Automatic deployment via GitHub Actions

## Tech Stack

- **Jekyll**: Static site generator
- **GitHub Pages**: Hosting platform
- **Ruby**: Programming language for Jekyll
- **HTML/CSS/JavaScript**: Frontend technologies
- **GitHub Actions**: CI/CD pipeline

## Getting Started

### Prerequisites

- Ruby 3.2 or higher
- Bundler gem
- Git

### Local Development

1. **Clone the repository**
   ```bash
   git clone https://github.com/savitharaghunathan/savitharaghunathan.github.io.git
   cd savitharaghunathan.github.io
   ```

2. **Install dependencies**
   ```bash
   bundle install
   ```

3. **Run the development server**
   ```bash
   bundle exec jekyll serve
   ```

4. **Open your browser**
   Navigate to `http://localhost:4000` to view your site.

### Configuration

Edit `_config.yml` to customize your site:

```yaml
# Site settings
title: "Your Name"
email: "your-email@example.com"
description: "Your site description"
url: "https://yourusername.github.io"
github_username: "yourusername"
```

## Project Structure

```
├── _config.yml          # Jekyll configuration
├── _layouts/            # HTML templates
│   ├── default.html     # Main layout
│   ├── post.html        # Blog post layout
│   └── podcast.html     # Podcast episode layout
├── _includes/           # Reusable components
├── _pages/              # Static pages
│   ├── about.md         # About page
│   ├── blog.md          # Blog listing page
│   └── podcasts.md      # Podcasts listing page
├── _posts/              # Blog posts (YYYY-MM-DD-title.md)
├── _podcasts/           # Podcast episodes
├── assets/              # Static assets
│   ├── css/             # Stylesheets
│   ├── js/              # JavaScript files
│   └── images/          # Images
├── Gemfile              # Ruby dependencies
└── README.md            # This file
```

## Adding Content

### Blog Posts

Create new blog posts in the `_posts/` directory with the format `YYYY-MM-DD-title.md`:

```markdown
---
layout: post
title: "Your Post Title"
date: 2024-01-15
categories: [Category]
tags: [tag1, tag2]
excerpt: "Brief description of your post"
---

Your blog post content here...
```

### Podcast Episodes

Create new podcast episodes in the `_podcasts/` directory:

```markdown
---
layout: podcast
title: "Episode Title"
date: 2024-01-15
duration: "15:30"
description: "Episode description"
audio_url: "https://example.com/audio.mp3"
tags: [tag1, tag2]
show_notes: |
  Your show notes here...
---

Episode content here...
```

### Pages

Add new pages in the `_pages/` directory:

```markdown
---
layout: default
title: "Page Title"
permalink: /page-url/
---

Page content here...
```

## Customization

### Styling

The main stylesheet is located at `assets/css/main.css`. The design uses:

- CSS Grid and Flexbox for layouts
- CSS Custom Properties for theming
- Mobile-first responsive design
- Smooth transitions and animations

### JavaScript

Custom JavaScript is in `assets/js/main.js` and includes:

- Mobile navigation toggle
- Smooth scrolling
- Lazy loading for images
- Copy-to-clipboard functionality for code blocks

## Deployment

### GitHub Pages

This site is configured for automatic deployment to GitHub Pages:

1. Push your changes to the `main` branch
2. GitHub Actions will automatically build and deploy your site
3. Your site will be available at `https://yourusername.github.io`

### Manual Deployment

If you prefer manual deployment:

```bash
bundle exec jekyll build
```

The built site will be in the `_site/` directory.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Support

If you encounter any issues or have questions:

1. Check the [Jekyll documentation](https://jekyllrb.com/docs/)
2. Search existing [GitHub issues](https://github.com/savitharaghunathan/savitharaghunathan.github.io/issues)
3. Create a new issue with detailed information

## Acknowledgments

- [Jekyll](https://jekyllrb.com/) for the static site generator
- [GitHub Pages](https://pages.github.com/) for hosting
- [Inter font](https://rsms.me/inter/) for typography
- [Cursor AI](https://cursor.sh/) for website development assistance
- The open source community for inspiration and tools

---

Built with ❤️ using Jekyll and GitHub Pages 