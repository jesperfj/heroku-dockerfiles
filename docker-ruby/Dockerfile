FROM heroku/docker-base

ENV RUBY_ENGINE 2.2.1
ENV BUNDLER_VERSION 1.7.12
ENV NODE_ENGINE 0.10.38

ENV PATH /app/heroku/ruby/bin:/app/heroku/bundler/bin:/app/heroku/node/bin:$PATH
ENV GEM_PATH=/app/heroku/bundler:$GEM_PATH
ENV GEM_HOME=/app/vendor/bundle

RUN mkdir -p /app/heroku/ruby \
 && mkdir -p /app/heroku/bundler \
 && mkdir -p /app/vendor/bundle \
 && mkdir -p /app/heroku/node \
 && curl -s https://s3-external-1.amazonaws.com/heroku-buildpack-ruby/cedar-14/ruby-$RUBY_ENGINE.tgz \
  | tar xz -C /app/heroku/ruby \
 && curl -s https://s3-external-1.amazonaws.com/heroku-buildpack-ruby/bundler-$BUNDLER_VERSION.tgz \
  | tar xz -C /app/heroku/bundler \
 && curl -s https://s3pository.heroku.com/node/v$NODE_ENGINE/node-v$NODE_ENGINE-linux-x64.tar.gz \
  | tar --strip-components=1 -xz -C /app/heroku/node


ADD src /app
