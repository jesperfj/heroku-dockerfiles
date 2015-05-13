FROM heroku/node:0.12.2

ONBUILD COPY package.json /app/
ONBUILD RUN npm install
ONBUILD COPY . /app/
