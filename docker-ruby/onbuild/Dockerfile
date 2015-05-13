FROM heroku/ruby:2.2.1

ONBUILD COPY Gemfile /app/
ONBUILD COPY Gemfile.lock /app/
ONBUILD RUN bundle install
ONBUILD COPY . /app/
