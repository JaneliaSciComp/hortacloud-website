# HortaCloud Website

This is the website for [HortaCloud](https://github.com/JaneliaSciComp/hortacloud).

It is built using the [Hugo](https://gohugo.io) static site generator, and uses the [Docsy theme](https://docsy.dev).

## Development

First, [install Hugo](https://gohugo.io/getting-started/installing). On Mac, this is just `brew install hugo`.

To compile this site locally, you must fetch submodules recursively.

Clone the repository like this:

    git clone --recurse-submodules --depth 1 git@github.com:JaneliaSciComp/hortacloud-website.git

Or if you already cloned, do this:

    git submodule update --init --recursive

Then install dependencies:

    npm install

Now you can compile the code and run a development server:

    hugo server

## Release

When you push to the main branch, the code will automatically be built and deployed to the production website running at [https://hortacloud.janelia.org/](https://hortacloud.janelia.org).

## Troubleshooting

Try [this fix](https://wowchemy.com/docs/hugo-tutorials/troubleshooting/#error-failed-to-resolve-output-format), if you see this error:

    Error: from config: failed to resolve output format "print" from site config

