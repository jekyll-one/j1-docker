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

# Ruby Base image (Alpine Linux)
# ------------------------------------------------------------------------------
FROM ruby:alpine3.7

# Author
# ------------------------------------------------------------------------------
LABEL maintainer "Juergen Adams <juergen@jekyll-one.com>"

# Place HELPER
# ------------------------------------------------------------------------------
COPY copy /

# RUBY Environment
# ------------------------------------------------------------------------------
ENV BUNDLE_HOME=/usr/local/bundle
ENV BUNDLE_APP_CONFIG=/usr/local/bundle
ENV BUNDLE_BIN=/usr/local/bundle/bin
ENV GEM_BIN=/usr/gem/bin
ENV GEM_HOME=/usr/gem

# IMAGE Environment
# ------------------------------------------------------------------------------
ENV JEKYLL_VAR_DIR=/var/jekyll
ENV JEKYLL_DOCKER_TAG=<%= @meta.tag %>
ENV JEKYLL_VERSION=<%= @meta.release?? @meta.release : @meta.tag %>
ENV JEKYLL_DOCKER_COMMIT=<%= `git rev-parse --verify HEAD`.strip %>
ENV JEKYLL_DOCKER_NAME=<%= @meta.name %>
ENV JEKYLL_DATA_DIR=/srv/jekyll
ENV JEKYLL_BIN=/usr/jekyll/bin
ENV JEKYLL_ENV=development

# System Environment
# ------------------------------------------------------------------------------
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV TZ=Europe/Berlin
ENV PATH="$JEKYLL_BIN:$PATH"
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US

# USER Environment
# ------------------------------------------------------------------------------
<% if @meta.env? %>
  ENV <%= @meta.env %>
<% end %>

# MAIN Environment
# ------------------------------------------------------------------------------
env VERBOSE=false
env FORCE_POLLING=false
env DRAFTS=false

# USER Packages
# ------------------------------------------------------------------------------
<% if @meta.packages? %>
  RUN apk --no-cache add <%= @meta.packages %>
<% end %>

# DEV libraries
# ------------------------------------------------------------------------------
RUN apk --no-cache add \
  zlib-dev \
  build-base \
  libxml2-dev \
  libxslt-dev \
  readline-dev \
  libffi-dev \
  yaml-dev \
  cmake

# MAIN libraries
# ------------------------------------------------------------------------------
RUN apk --no-cache add \
  linux-headers \
  less \
  zlib \
  libxml2 \
  readline \
  libxslt \
  libffi \
  git \
  nodejs \
  tzdata \
  shadow \
  bash \
  su-exec \
  nodejs-npm \
  libressl \
  yarn

# PYTHON Package
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

# RUBY GEMs - UPDATE
# ------------------------------------------------------------------------------
RUN yes | gem update --system

# RUBY GEMs - MAIN
# ------------------------------------------------------------------------------
# Prevent vendored bundles to fail.
# See: https://github.com/rubygems/rubygems/issues/2062
RUN gem install bundler
RUN gem install jekyll -v<%= @meta.release?? \
  @meta.release : @meta.tag %> -- \
  --use-system-libraries

# RUBY GEMs - USER
# ------------------------------------------------------------------------------
<% if @meta.gems? %>
  # Use system-libraries for all RubyGem to speedup slow builts of Rubies
  # using native extensions (like Nokogiri)
  RUN gem install <%=@meta.gems %> -- \
    --use-system-libraries
<% end %>

# SYSTEM - Set user and group
# ------------------------------------------------------------------------------
RUN addgroup -Sg 1000 jekyll
RUN adduser  -Su 1000 -G jekyll jekyll
RUN addgroup -Sg 5555 cifs
RUN usermod  -aG cifs jekyll

# PREPARE - CREATE Application folders
# ------------------------------------------------------------------------------
RUN mkdir -p $JEKYLL_VAR_DIR
RUN mkdir -p $JEKYLL_DATA_DIR
RUN chown -R jekyll:jekyll $JEKYLL_DATA_DIR
RUN chown -R jekyll:jekyll $JEKYLL_VAR_DIR
RUN chown -R jekyll:jekyll $BUNDLE_HOME

# PREPARE - CLEAN Application folders
# ------------------------------------------------------------------------------
RUN rm -rf /root/.gem
RUN rm -rf /home/jekyll/.gem
RUN rm -rf $BUNDLE_HOME/cache
RUN rm -rf $GEM_HOME/cache

# RUN - Application startup
# ------------------------------------------------------------------------------
CMD ["jekyll", "--help"]
ENTRYPOINT ["/usr/jekyll/bin/entrypoint"]
WORKDIR /srv/jekyll
VOLUME  /srv/jekyll
ONBUILD RUN virtualenv /env && /env/bin/pip install -r /usr/python/requirements.txt
EXPOSE 35729
EXPOSE 40000
