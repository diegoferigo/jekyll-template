# jekyll-template

This repository provides a simple self-contained static website generated with [jekyll](https://github.com/jekyll/jekyll), and the toolchain to generate and serve it locally.

The only dependencies required to use this repository are `git` and `docker`.

The `docker` image contains the minimum amount of dependencies, mainly ruby gems, needed to generate the default website provided by jekyll. After this, all the website configuration is demanded to `bundler`.

This means that the project's `Gemfile` should include all the dependencies needed to its execution (`jekyll` included). This potentially allows to avoid the usage of the provided `docker` image that might be useful in particular cases.

## Build the docker image

Clone this repository, start the docker daemon, and then execute:

```sh
docker build --rm -t loc2/jekyll .
```

Sooner or later this image will be uploaded to dockerhub.

## HowTo

The provided docker image can be used in the following way:

```sh
docker run -it --rm \
           -v $(pwd)/jekyll:/srv/jekyll/www \
           -p 4000:4000 \
           --name jekyll \
           loc2/jekyll \
           {{command}}
```

Where `{{command}}` is:

* `new`: generates a new jekyll website
* `build`: builds a website from the `/jekyll` folder
* `serve`: exposes the website to the host at `localhost:4000`

### Example: Generate a default website

This repository already provides a `/jekyll` folder containing a working static website with Bootstrap 4, a default theme, and a bunch of jekyll plugins.

If, instead, you want to exploit this workflow for starting from scratch with the default jekyll website, execute

```sh
cd jekyll-template
rm -r jekyll
mkdir jekyll
docker run -it --rm \
           -v $(pwd)/jekyll:/srv/jekyll/www \
           --name jekyll \
           loc2/jekyll \
           new
```

### Example: Use an existing jekyll website

If you already have an existing website created with jekyll (even hosted in another repo) and you want to exploit this workflow, substitute the `/jekyll` folder with your own. Then, serve it to the host executing:

```sh
cd jekyll-template
docker run -it --rm \
           -v $(pwd)/jekyll:/srv/jekyll/www \
           -p 4000:4000 \
           --name jekyll \
           loc2/jekyll \
           serve
```

Then browse to `localhost:4000`.

## Mainteinance guidelines

### This repo

This repository is a template, something you can use as starting point to develop a website. We try to always keep it a rolling release for what concern tools and plugins.

There are two branches:

* `master`: it contains the template
* `gh-pages`: it is an orphan branch that contains a copy of the `/jekyll` folder present in the `master` branch, and it serves as demo website

The maintainers of this repository should commit in the `master` branch all the developed files and configurations (the `/jekyll` folder). The `gh-pages` branch should instead be populated only by the files contained in `/jekyll`, that may or may not contain the `/_site` folder depending if you want to let GitHub generate the website by their servers.

We can exploit `subtree` in order to quickly commit in `gh-pages` only a subfolder of the `master` branch:

```
git subtree push --prefix jekyll origin gh-pages
```

For future reference, the command for a force push of a subtree is:

```
git push origin `git subtree split --prefix jekyll master`:gh-pages --force
```

### A website that uses this template

There are plenty of possible workflows in order to use this template for a website.

The main advantage of having a template that can be used for many websites is the possibility to split infrastucture, hosted in this `jekyll-template` repo, and the posts and optionally a different theme, hosted for instance by the `username.github.io` repository.

The git structure of the `username.github.io` repository we are currently using, keeping in mind that we want to have an easy way to maintain it aligned with possible infrastructure improvement provided by the template, is the following:

* `master`: the actual website (that as for the demo, may or may not contain the `/_site` folder)
* `template`: a mirror of `jekyll-template:master`

For every new commit in `jekyll-template`, the mirror `username.github.io:template` should be updated, and `username.github.io:master` rebased on top of it. If the infrastructure and theme plus posts are kept enough independent, conflicts should be rare.
