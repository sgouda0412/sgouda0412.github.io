---
title: "Setting Up a Website in Hugo: A Complete Guide"
date: 2025-03-20T12:00:00+01:00
author: Santosh Kumar Gouda
draft: false
tags: ["hugo", "web development", "github pages"]
---

Hugo is one of the most popular open-source static site generators available today. Known for its blazing-fast build times and flexibility, Hugo makes creating websites simple and enjoyable. This guide will walk you through setting up a Hugo website with the Archie theme and deploying it to GitHub Pages.

## Prerequisites

- Basic knowledge of Git and command line
- GitHub account
- Text editor of your choice

## Installing Hugo

Start by downloading and installing Hugo:

```bash
wget https://github.com/gohugoio/hugo/releases/download/v0.125.7/hugo_extended_0.125.7_Linux-64bit.tar.gz
tar -xvzf hugo_extended_0.125.7_Linux-64bit.tar.gz
sudo mv hugo /usr/local/bin/
hugo version
```

> **Note:** 
> - For Mac users, you can install Hugo with Homebrew: `brew install hugo`
> - For Windows users, you can use Chocolatey: `choco install hugo-extended`

## Creating a New Hugo Site

Create a new Hugo site and initialize Git:

```bash
hugo new site my-hugo-blog
cd my-hugo-blog
git init
```

## Adding the Archie Theme

Add the Archie theme as a Git submodule and configure it:

```bash
git submodule add https://github.com/athul/archie.git themes/archie
cp themes/archie/exampleSite/config.toml .
rm -rf hugo.toml
```

## Updating Site Configuration

Edit the `config.toml` file to customize your site:

```bash
vi config.toml
```

Update these key settings:

```toml
baseURL = "https://your-username.github.io/"
title = "My Hugo Blog"
theme = "archie"
```

You can further customize your site by adjusting other parameters in the config file:

```toml
# For light/dark mode toggle
enableEmoji = true
pygmentsstyle = "monokai"
pygmentscodefences = true

# Author details
[params]
  mode = "auto"
  featherIconsCDN = true
  subtitle = "Clean and minimal personal blog theme"

# Social media links
[[params.social]]
  name = "GitHub"
  icon = "github"
  url = "https://github.com/your-username"

[[params.social]]
  name = "Twitter"
  icon = "twitter"
  url = "https://twitter.com/your-username"
```

## Adding Content

Copy example content and create your first post:

```bash
cp -r themes/archie/exampleSite/content .
hugo new posts/my-first-post.md
```

Open the newly created post in your text editor and modify the front matter:

```markdown
---
title: "My First Post"
date: 2025-03-20T12:00:00+01:00
draft: false
tags: ["hugo", "web development"]
---

Hello world! This is my first Hugo blog post.
```

## Testing Locally

Start the Hugo development server to preview your site:

```bash
hugo server -D
```

Visit http://localhost:1313 in your browser to see your site. The `-D` flag allows you to view draft posts.

## Setting Up GitHub Repository

1. Go to GitHub.com
2. Create a new repository with the name format: `your-username.github.io`

Configure your local repository:

```bash
git remote add origin https://github.com/your-username/your-username.github.io.git
git branch -M main
git add .
git commit -m "Initial commit"
git push -u origin main
```

## Creating GitHub Pages Branch

Create and push a gh-pages branch:

```bash
git checkout -b gh-pages
git push origin gh-pages
git checkout main
```

## Building and Deploying

Build your site and push to the gh-pages branch:

```bash
hugo
cd public
git init
git remote add origin https://github.com/your-username/your-username.github.io.git
git checkout -b gh-pages
git add .
git commit -m "Deploy Hugo site"
git push origin gh-pages --force
```

## Accessing Your Live Site

Once deployed, your site will be available at:
https://your-username.github.io/

## Automating Deployment with GitHub Actions

For a more streamlined workflow, you can set up GitHub Actions to automatically build and deploy your site whenever you push changes:

1. Create a `.github/workflows` directory in your repository
2. Add a file named `hugo-deploy.yml` with the following content:

```yaml
name: Deploy Hugo site
on:
  push:
    branches:
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: true
          fetch-depth: 0
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true
      - name: Build
        run: hugo --minify
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

## Adding a Custom Domain (Coming Soon)

To use a custom domain with your Hugo site:

1. Purchase a domain from a domain registrar
2. Create a file named `CNAME` in the `static` directory of your Hugo project
3. Add your domain to the `CNAME` file (e.g., `www.yourdomain.com`)
4. Configure DNS settings with your domain provider
5. Enable HTTPS in your GitHub repository settings

A more detailed guide on custom domain setup will be covered in a future post.

## Customization Tips

- Replace `your-username` with your actual GitHub username in all commands
- Edit your posts in the `content/posts/` directory
- Customize theme settings in `config.toml`
- Run `hugo server -D` locally to preview changes before deploying
- Explore Hugo's themes directory for alternative themes

## Conclusion

You now have a fully functioning Hugo website deployed to GitHub Pages! Hugo offers powerful features for content management, and with the simplicity of GitHub Pages, you can focus on creating content while enjoying free, reliable hosting.

Happy blogging!

*Last updated: March 27, 2025*
