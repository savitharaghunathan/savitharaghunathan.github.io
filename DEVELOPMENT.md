# Local Development Guide

This guide details the tech stack, configuration, local setup, and deployment processes for Savitha Raghunathan's personal portfolio website.

## Tech Stack

- **Jekyll**: Static site generator
- **GitHub Pages**: Hosting platform
- **Ruby**: Programming language for Jekyll
- **HTML/CSS/JavaScript**: Frontend technologies
- **GitHub Actions**: CI/CD pipeline

## Local Development

### Prerequisites

- Ruby 3.2 or higher
- Bundler gem
- Git

### Running Locally

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

4. **Verify the site**
   Open `http://localhost:4000` in your web browser.

## Configuration

Edit `_config.yml` at the root of the repository to customize parameters such as the site title, description, socials, and integrations:

```yaml
# Site settings
title: Savitha Raghunathan
first_name: Savitha
last_name: Raghunathan
description: "Welcome to my personal website! :)"
url: https://savitharaghunathan.github.io
```

## Deployment

The website uses GitHub Actions for CI/CD deployment:
1. Pushing commits to the `main` branch automatically triggers the build and deploy workflow (`.github/workflows/jekyll.yml`).
2. GitHub Actions compiles the Jekyll site and deploys it directly to GitHub Pages.

To build manually:
```bash
bundle exec jekyll build
```
The compiled static website will be available in the `_site/` directory.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
