---
title: "Hugo Plus GitHub Equals to Hello World"
date: 2019-09-22T13:05:18+02:00
draft: false
coverMeta: out
tags:
- content
- devops
- hugo
comments: true
---
---

# Objective

I want to publish some content on my blog using some content generator, and I don't want to hire a hosting. I can use [Wordpress](http://wordpress.com), but I prefer to use some technologies that let me know new ways of publishing content.

So, I will use [Hugo](https://gohugo.io/) for this purpose. There are a lot of tools like [Jekyllrb](https://jekyllrb.com), [Ghost](https://ghost.org), [Grav](https://getgrav.org), [Pelican](http://getpelican.com) and [more alternatives](https://alternativeto.net/software/hugo/).

The last step will be set up GitHub actions. When I push the editor branch, on master must be published the public content.

## Creating the project.

The first thing that I need to do is install the dependencies, prepare the repository and commit the base configuration on the editor branch.

{{< codeblock "shell" >}}
# Install Hugo on a mac using Brew
brew install hugo
# Create new Hugo site
hugo new site Blog
# Initialize git repository
cd Blog & git init
# Add all files for initial commit on editor branch
git checkout -b editor
git add *
git commit -m "Initial commit"
{{< /codeblock >}}
The next step is to select a [theme](hugo-tranquilpeak-theme) and create some content (like this post).
{{< codeblock "shell" >}}
# Add theme as git submodule
git submodule add https://github.com/kakawait/hugo-tranquilpeak-theme.git themes/hugo-tranquilpeak
# Copy default configuration and update with my custom parameters
cp themes/tranquilpeak/exampleSite/config.toml .
vim config.toml
# Commit this changes
git add .gitmodules themes/hugo-tranquilpeak config.toml
git commit "Add Hugo Tranquilpeak theme and update configuration"
# Create this post, and add some summary content
hugo new content/post/Hugo-plus-Github-equals-to-Hello-World.md
git add content/
git commit -m "Add first post"
{{< /codeblock >}}

And that's setup Hugo with a first post.

## Setting up GitHub & GitHub actions

Tip: Create a repository on GitHub before anything.

As said before, I want to publish automatically the changes when I push the editor branch to Github.
Hugo creates the content on the "public" folder, so, I need to make this folder as [an orphan branch](https://git-scm.com/docs/git-checkout/#git-checkout---orphanltnewbranchgt). I will follow [these steps](https://gohugo.io/hosting-and-deployment/hosting-on-github/#preparations-for-gh-pages-branch) but with some changes.
{{< codeblock "shell" >}}
# Ensure that I am on editor branch
git branch
# Add GitHub repository as origin
git remote add origin git@github.com:robertobermejo/robertobermejo.github.io.git
# Push editor branch to github
git push origin editor
# Ignore public folder
echo "public" >> .gitignore
# Initialize master branch
git checkout --orphan master
git reset --hard
# In my case, I need to remove themes folder because I use this as submodule
rm -rf themes
# Commit and push master branch
git commit --allow-empty -m "Initializing master branch"
git push origin master
# Change to editor branch and restore theme folder
git checkout editor
git submodule update
{{< /codeblock >}}

If you want to use a custom domain on GitHub, you must create and commit a file on master branch name "CNAME". The content of this file must be the domain that you want to use. On my case
{{< codeblock "text" >}}
robertobermejo.es
{{< /codeblock >}}

When you access to GitHub repository settings page, the following option appears.
{{< image classes="fancybox" src="/github/page_settings_before.png" thumbnail-width="80%" title="Image of GitHub settings before activating that option" >}}
It's needed that mark master as default branch for the site
{{< image classes="fancybox" src="/github/page_settings_selected.png" thumbnail-width="80%" title="Image of GitHub selecting the master branch as the branch for github pages" >}}
Finally, if the CNAME file is uploaded, the following picture should appear
{{< image classes="fancybox" src="/github/page_settings_domain.png" thumbnail-width="80%" title="Image of GitHub settings after activating the branch and setting the custom domain option" >}}

Now, It's time to configure GitHub actions.
For that, we need a docker image. The "official" image that I see on [Docker hub](https://hub.docker.com/r/gohugoio/hugo) "it's outdated, so I uploaded a new image on [my account](https://hub.docker.com/r/robertobermejo/hugo).

As I want to use a custom image, It's needed to add the file ".github/workflow/main.yml" on the repository with the following code:
{{< codeblock "yaml" >}}
name: CI

on:
  push:
    branches:
    - editor
    - content/*

jobs:
  build:

    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v1
    - name: Build the site in the robertobermejo/hugo container
      run: |
        docker run -v ${{ github.workspace }}:/srv/ robertobermejo/hugo:latest -s /srv

{{< /codeblock >}}

Don't forget to commit the changes.
{{< codeblock "shell" >}}
git add .github
git commit -m "Add github actions setup"
git push origin editor
{{< /codeblock >}}

Some minutes later, GitHub's actions should have the following appearance.

{{< image classes="fancybox" src="/github/actions_configured.png" thumbnail-width="80%" title="Image of github settings after activating the branch and setting the custom domain option" >}}

And that's all.
