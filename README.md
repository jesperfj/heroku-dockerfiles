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

# Running apps that pick up changes immediately

For interpreted languages like Node.js, Ruby and PHP, it is common to configure your local environment to pick up changes to files in your project directory without having to restart the server or rebuild anything. This makes for a very snappy development experience.

In this proposal, you accomplish this with a separately derived container. It's currently only implemented for Node.js. In your Node app directory, do:

```
docker run -it -v `pwd`:/app/src -p 5000:5000 heroku/node-inplace:0.12.2 npm install
```

This will pull down dependencies (without you having to install node or npm locally, it runs in the container). Now you can start the server:

```
docker run -it -v `pwd`:/app/src -p 5000:5000 heroku/node-inplace:0.12.2
```

heroku/node-inplace installs nodemon and runs it with no arguments as CMD. nodemon will read package.json to find out what to execute.
