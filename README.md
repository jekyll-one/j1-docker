# J1 Docker

J1 Docker is a `Docker Image` to manage all development and run-time
processes for J1 Template. The image contains, beside of Jekyll and
a full set of RubyGems, all development dependencies like the languages
Ruby, Python and NodeJS ready to use.

## Image Types

*   `jekyllone/j1dev`: Development image
*   `jekyllone/j1app`: Web Application image


### Development

The development image `jekyllone/j1dev` can be used to run all development
processes for:

*   `j1_template_mde_dev`, the Developer package for J1 on Github
*   `j1_template_mde`, a RubyGem to create run-time versions


```sh
export JEKYLL_VERSION=3.8
docker run --rm \
  --volume=$PWD:/srv/jekyll \
  -p 35729:35729 -p 40000:40000 \
  -it jekyllone/j1dev:latest \
  jekyll serve --incremental
```


### RubyGems

J1 Docker will attempt to install any dependencies that you list inside
of your `Gemfile`, matching the versions you have in your `Gemfile.lock`,
including Jekyll if you have a version that does not match the version of
the image you are using (you should be doing `gem "jekyll", "~> 3.8"` so
that minor versions are installed if you use say image tag "3.7.3").

### Update from a Gemfile

If you provide a `Gemfile` and would like to update your `Gemfile.lock`
you can run:

```sh
export JEKYLL_VERSION=3.8
docker run --rm \
  --volume="$PWD:/srv/jekyll" \
  -it jekyllone/j1dev:latest \
  bundle update
```


### LiveReload

J1 images support [jekyll-reload](https://rubygems.org/gems/jekyll-reload).
All you need do is to [configure it according to your needs](http://www.rubydoc.info/gems/jekyll-reload/).

```sh
export JEKYLL_VERSION=3.8
docker run --rm \
  --volume=$PWD:/srv/jekyll \
  -p 35729:35729 -p 40000:40000 \
  -it jekyllone/j1dev:latest \
  jekyll serve --incremental --livereload
```

## Build Images

You can build images or any specific tag of an image running

```sh
bundle exec docker-template build
```

or

```sh
bundle exec docker-template build repo:tag
```

It's simple like that to build images!

Example:

```sh
bundle exec docker-template build j1dev:3.8 --no-push
```

### Reset a Build

```sh
bundle exec docker-template clean
```

### Remove <none> images after Build

This will print you all untagged images

```sh
docker images ls -a | grep "^<none>" | awk "{print $3}"
```

This filtering also works for dangling volumes. To remove all those images
run:

```sh
docker rm $(docker images ls -a | grep "^<none>" | awk "{print $3}")
```


## Explore an Image

To have a look inside an image, run a container using a bash (shell):


```sh
docker run --rm \
  --name j1_develop \
  --hostname j1_develop \
  --volume=$PWD:/srv/jekyll \
  -it jekyllone/j1dev:3.8 bash
```
=======

This will print you all untagged images

```sh
docker images ls -a | grep "^<none>" | awk "{print $3}"
```

This filtering also works for dangling volumes. To remove all those images
run:

```sh
docker rm $(docker images ls -a | grep "^<none>" | awk "{print $3}")
```


## Explore an Image

To have a look inside an image, run a container using a bash (shell):


```sh
docker run --rm \
  --name j1_develop \
  --hostname j1_develop \
  --volume=$PWD:/srv/jekyll \
  -it jekyllone/j1dev:3.8 bash
```
