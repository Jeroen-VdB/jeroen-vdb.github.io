---
title: "Embracing Branding Control with Jekyll"
description: "Moving from Medium to Jekyll for better branding control and flexibility in my freelance journey. Learn how to set up a Jekyll blog with GitHub Pages and the Minimal Mistakes theme."
categories:
  - Blog
---

After much thought, I'm bidding farewell to Medium, not because it's lacking, but because I'm venturing into freelance and crave more control over my blog's branding. While Medium has served me well, I'm drawn to Jekyll for its flexibility and customization. With Jekyll, I'll have ownership of my content and the freedom to design my site to reflect my style

Setting up Jekyll in GitHub is made very accessible and has great documentation: https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll

Starting with the [Minimal Mistakes Repository template](https://github.com/new?template_name=mm-github-pages-starter&template_owner=mmistakes) I was able to set up my blog in less than a day's work.

After you cloned the repository you can build and run the blog with [Bundler](https://bundler.io/) using the `bundle exec jekyll serve` command.

Keeping the template up to date is also made very easy as you only have config files to override settings instead of cloning the full source code into your own repository. Simply run `bundle update` to use the latest version.

Next, you'll want to configure the `_config.yml` file to update the site title, social links, date format and other settings.

To align with my logo colors, I also have an `assets/css/main.scss` file which overrides theme variables like `$primary-color` and `$background-color`.

Inside the `_pages` folder you can update, for example, the content of the 404 page not found or about page.

And now it's time to start writing articles. Under the `_posts` folder you can add markdown files with a date included in the file name and at the top of the file a short YAML part to define page metadata. Like title, categories and tags. Using VS Code or Obsidian it's fairly easy to write nicely formatted articles using markdown.

As a last step you can configure your domain name in the repository settings and we're done! 😁


