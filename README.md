A slightly different approach to heroku-docker. This approach tracks the language images maintained by Docker itself (and partners like Joyent). We maintain two images in Docker Hub for every language and every version, a 'standard' and an ONBUILD flavor.

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
