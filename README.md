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

This image supports [jekyll-reload](https://rubygems.org/gems/jekyll-reload),
all you need do is to [configure it according to your needs](http://www.rubydoc.info/gems/jekyll-reload/).

```sh
export JEKYLL_VERSION=3.8
docker run --rm \
  --volume=$PWD:/srv/jekyll \
  -p 35729:35729 -p 40000:40000 \
  -it jekyllone/j1dev:latest \
  jekyll serve --incremental --livereload
```

## Building Our Images

You can build our images or any specific tag of an image with
`bundle exec docker-template build` or `bundle exec docker-template build repo:tag`,
yes it's that simple to build our images; even if it looks complicated
it's not.

## Contributing

*   Fork the current repo; `bundle install`
*   `opts.yml` holds most of the versions, and gems.
*   Test your image manually `script/debug` will help you with that.
*   Ensure that your intended changes work as they're supposed to.
*   Ship a pull request if you wish to have it reviewed!
