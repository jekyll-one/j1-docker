# ------------------------------------------------------------------------------
# ~/repos/dev/Dockerfile
# J1 Docker Development Image based on Alpine Linux (https://alpinelinux.org/)
#
# Product/Info:
# https://jekyll-one.com
#
# Copyright (C) 2018 Juergen Adams
#
# J1 Template is licensed under the MIT License.
# See: https://github.com/jekyll-one/j1_template_mde/blob/master/LICENSE
# ------------------------------------------------------------------------------
# NOTE:
#
# ------------------------------------------------------------------------------

# BASE - Define base image Ruby|Alpine Linux
# ------------------------------------------------------------------------------
FROM ruby:alpine3.7

# SET - Author information
# ------------------------------------------------------------------------------
LABEL maintainer "Juergen Adams <juergen@jekyll-one.com>"

# COPY - Wrapper and helper scripts
# ------------------------------------------------------------------------------
COPY copy /

# RUBY - Environment
# ------------------------------------------------------------------------------
ENV BUNDLE_HOME=/usr/local/bundle
ENV BUNDLE_APP_CONFIG=/usr/local/bundle
ENV BUNDLE_BIN=/usr/local/bundle/bin
ENV GEM_BIN=/usr/gem/bin
ENV GEM_HOME=/usr/gem

# IMAGE - Environment
# ------------------------------------------------------------------------------
ENV JEKYLL_VAR_DIR=/var/jekyll
ENV JEKYLL_DOCKER_TAG=<%= @meta.tag %>
ENV JEKYLL_VERSION=<%= @meta.release?? @meta.release : @meta.tag %>
ENV JEKYLL_DOCKER_COMMIT=<%= `git rev-parse --verify HEAD`.strip %>
ENV JEKYLL_DOCKER_NAME=<%= @meta.name %>
ENV JEKYLL_DATA_DIR=/srv/jekyll
ENV JEKYLL_BIN=/usr/jekyll/bin
ENV JEKYLL_ENV=development
ENV LOCAL_BIN=/usr/local/bin

# SYSTEM - Environment
# ------------------------------------------------------------------------------
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV TZ=Europe/Berlin
ENV PATH="$JEKYLL_BIN:$PATH"
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US

# MAIN - Environment
# ------------------------------------------------------------------------------
ENV VERBOSE=false
ENV FORCE_POLLING=false
ENV DRAFTS=false
ENV J1DOCKER=true

# USER - Environment
# ------------------------------------------------------------------------------

# USER - Packages
# ------------------------------------------------------------------------------
<% if @meta.packages? %>
  RUN apk --no-cache add <%= @meta.packages %>
<% end %>

# DEV - Libraries
# ------------------------------------------------------------------------------
RUN apk --no-cache add \
  zlib-dev \
  libxml2-dev \
  libxslt-dev \
  readline-dev \
  libffi-dev \
  yaml-dev

# DEV - Applications
# ------------------------------------------------------------------------------
RUN apk --no-cache add \
  build-base \
  cmake

# MAIN - Libraries
# ------------------------------------------------------------------------------
RUN apk --no-cache add \
  linux-headers \
  zlib \
  libxml2 \
  libxslt \
  libffi \
  libressl

# MAIN - Applications
# ------------------------------------------------------------------------------
RUN apk --no-cache add \
  mc \
  bash \
  less \
  readline \
  tzdata \
  shadow \
  su-exec \
  git

# LANGUAGE - NodeJS
# ------------------------------------------------------------------------------
RUN apk --no-cache add \
nodejs \
nodejs-npm \
yarn

# LANGUAGE - Python
# ------------------------------------------------------------------------------
RUN apk add --update \
    python \
    python-dev \
    py-pip \
    build-base \
  && pip install --upgrade pip \
  && pip install virtualenv \
  && rm -rf /var/cache/apk/*

# RUBY GEMs - Disable RI|RDOC processing for all RubyGems for the image
# ------------------------------------------------------------------------------
RUN echo "gem: --no-ri --no-rdoc" > ~/.gemrc

# RUBYGEM - Update
# ------------------------------------------------------------------------------
RUN yes | gem update --system

# RUBYGEM - Main
# ------------------------------------------------------------------------------
# Prevent vendored bundles to fail.
# See: https://github.com/rubygems/rubygems/issues/2062
RUN gem install bundler
RUN gem install jekyll -v<%= @meta.release?? \
  @meta.release : @meta.tag %> -- \
  --use-system-libraries

# RUBYGEM user
# ------------------------------------------------------------------------------
<% if @meta.gems? %>
  # Use system-libraries for all RubyGem to speedup slow builts of Rubies
  # using native extensions (like Nokogiri)
  RUN gem install <%=@meta.gems %> -- \
    --use-system-libraries
<% end %>

# SETUP - Set user and group
# ------------------------------------------------------------------------------
RUN addgroup -Sg 1000 jekyll
RUN adduser  -Su 1000 -G jekyll jekyll
RUN addgroup -Sg 5555 cifs
RUN usermod  -aG cifs jekyll

# SETUP - CREATE Application folders
# ------------------------------------------------------------------------------
RUN mkdir -p $JEKYLL_VAR_DIR
RUN mkdir -p $JEKYLL_DATA_DIR
RUN chown -R jekyll:jekyll $JEKYLL_DATA_DIR
RUN chown -R jekyll:jekyll $JEKYLL_VAR_DIR
RUN chown -R jekyll:jekyll $BUNDLE_HOME

# SETUP - CLEAN Application folders
# ------------------------------------------------------------------------------
RUN rm -rf /root/.gem
RUN rm -rf /home/jekyll/.gem
RUN rm -rf $BUNDLE_HOME/cache
RUN rm -rf $GEM_HOME/cache

# RUN - Application startup
# ------------------------------------------------------------------------------
CMD ["j1", "--help"]
ENTRYPOINT ["/usr/jekyll/bin/entrypoint"]
WORKDIR /srv/jekyll
VOLUME  /srv/jekyll
ONBUILD RUN virtualenv /env && /env/bin/pip install -r /usr/python/requirements.txt
EXPOSE 35729
EXPOSE 40000
