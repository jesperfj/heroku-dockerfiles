A slightly different approach to heroku-docker. We maintain two images in Docker Hub for every language and every version, a 'standard' and an ONBUILD flavor.

The Dockerfile in a Ruby app, now will look like this:

```
FROM heroku/ruby-onbuild:2.2.1
```

and for Node:

```
FROM heroku/node-onbuild:0.12.2
```

Assuming you're standing inside [ruby-getting-started](https://github.com/heroku/ruby-getting-started) or [node-js-getting-started](https://github.com/heroku/node-js-getting-started) and you've added the Dockerfile above, you build with:

```
docker build -t $(basename `pwd`) .
```

This model does not do any file system mounting. If you want files generated as part of the build process to be saved back to your local directory, use `docker cp`, like this:

```
docker cp $(docker run -d $(basename `pwd`) echo):/app/Gemfile.lock .
```

You run your app with:

```
docker run -it -p 5000:5000 $(basename `pwd`)
```

If you want to vendor in more stuff, you are best off by moving away from the ONBUILD upstream in order to take advantage of caching. So say you want to add ImageMagick to your Node app. You'd copy in the instructions from the ONBUILD Dockerfile and derive from heroku/node instead. Now you can add dependencies in the beginning of the Dockerfile so you can take advantage of caching:

```
FROM heroku/node:0.12.2

RUN curl -s http://78.108.103.11/MIRROR/ftp/GraphicsMagick/1.3/GraphicsMagick-1.3.21.tar.gz | tar xvz -C /tmp
WORKDIR /tmp/GraphicsMagick-1.3.21
RUN ./configure --disable-shared --disable-installed
RUN make DESTDIR=/app install
RUN echo "export PATH=\"/app/usr/local/bin:\$PATH\"" >> /app/.profile.d/nodejs.sh
ENV PATH /app/usr/local/bin:$PATH
WORKDIR /app

# From ONBUILD Dockerfile

COPY package.json /app/
RUN npm install
COPY . /app/
```

