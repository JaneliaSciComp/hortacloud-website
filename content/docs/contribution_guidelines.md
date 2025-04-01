---
title: "Editing the documentation"
linkTitle: "Editing the documentation"
weight: 100
description: >
  How to contribute to the docs
---

We use [Hugo](https://gohugo.io/) to format and generate our website, the
[Docsy](https://github.com/google/docsy) theme for styling and site structure,
and [GitHub Pages](https://pages.github.com/) to manage the deployment of the site.

Hugo is an open-source static site generator that provides us with templates,
content organization in a standard directory structure, and a website generation
engine. You write the pages in Markdown (or HTML if you want), and Hugo wraps them up into a website.

All submissions, including submissions by project members, require review. We
use GitHub pull requests for this purpose. Consult
[GitHub Help](https://help.github.com/articles/about-pull-requests/) for more
information on using pull requests.

## Updating a single page

If you've just spotted something you'd like to change while using the docs, Docsy has a shortcut for you:

1. Click **Edit this page** in the top right hand corner of the page.
1. If you don't already have an up to date fork of the project repo, you are prompted to get one - click **Fork this repository and propose changes** or **Update your Fork** to get an up to date version of the project to edit. The appropriate page in your fork is displayed in edit mode.

## Previewing your changes locally

If you want to run your own local Hugo server to preview your changes as you work:

1. Follow the instructions to [install Hugo](https://gohugo.io/getting-started/installing/). It must be the **extended** version, which supports SCSS.
1. Fork the [HortaCloud website repo](https://github.com/JaneliaSciComp/hortacloud-website) locally:

    ```
    git clone --recurse-submodules --depth 1 https://github.com/JaneliaSciComp/hortacloud-website
    ```

1. Run `npm install` and `hugo server` in the site root directory. By default your site will be available at <http://localhost:1313/>. Now that you're serving your site locally, Hugo will watch for changes to the content and automatically refresh your site.
1. Continue with the usual GitHub workflow to edit files, commit them, push the
  changes up to your fork, and create a pull request.

## Creating an issue

If you've found a problem in the docs, but you're not sure how to fix it yourself, please create an issue in the [HortaCloud website repo](https://github.com/JaneliaSciComp/hortacloud-website/issues). You can also create an issue about a specific page by clicking the **Create Issue** button in the top right hand corner of the page.
