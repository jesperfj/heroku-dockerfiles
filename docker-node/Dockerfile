FROM heroku/docker-base

ENV PATH /app/heroku/node/bin:$PATH
ENV NODE_ENGINE 0.12.2

RUN mkdir -p /app/heroku && \
    mkdir -p /app/.profile.d && \
    curl -s https://s3pository.heroku.com/node/v$NODE_ENGINE/node-v$NODE_ENGINE-linux-x64.tar.gz | tar xvz -C /tmp && \
    mv /tmp/node-v$NODE_ENGINE-linux-x64 /app/heroku/node

ADD . /app
