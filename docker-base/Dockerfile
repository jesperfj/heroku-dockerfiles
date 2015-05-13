FROM heroku/cedar:14

# Procfile runner is currently one point of divergence from production.
# We install forego to be used for non-production runs. When deployed to
# heroku, forego will not be available. Heroku uses a different mechanism
# for running and supervising the process.

RUN curl -s https://godist.herokuapp.com/projects/ddollar/forego/releases/current/linux-amd64/forego > /usr/local/bin/forego && \
    chmod +x /usr/local/bin/forego

# Ideally we use the app user for everything, but ADD and COPY don't respect it
# and it messes up the Dockerfile structure to try to compensate for it. It looks
# like this will be fixed soon: https://github.com/docker/docker/pull/10775

# RUN useradd -d /app -m app
# USER app

ENV HOME /app
ENV PORT 5000

WORKDIR /app

# Not sure this is necessary unless we will have collaborating containers that
# will make requests to the app's container

EXPOSE 5000

CMD ["forego", "start"]
