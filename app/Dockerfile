# # Stretch
# # FROM node:12.16.1-stretch

# # Alpine
# FROM node:12.16.1-alpine3.11

# # EXPOSE 3000

# # Install all apt packages up top in one line, before copying files into container
# # RUN apt-get update && apt-get install [package]

# WORKDIR /usr/src/app

# # COPY package.json package-lock.json* ./
# COPY package*.json ./

# RUN npm install && npm cache clean --force

# # Change user to node
# # Never change to node user when running npm i or other root operations
# # Can only run CMD, RUN, and ENTRY_POINT
# # WORKDIR always uses root user
# # Instead use: RUN mkdir [folder name] && chown -R node:node .
# # To get into container as root: > docker-compose exec -it u root sh
# USER node

# COPY --chown=node:node . .

# # Add tini through the Dockerfile if you don't have access to the code
# # RUN apk add --no-cache tini
# # ENTRYPOINT [ "/sbin/tini", "--" ]
# # CMD [ "node", "./bin/www" ]

# CMD [ "node", "./bin/www" ]

# ----------------------------------------------------------------------------------------

# fill in the blanks to dockerize this node app
FROM node:12.16.1-alpine3.11

EXPOSE 3000

RUN apk add --no-cache tini

WORKDIR /app

COPY package*.json ./

RUN npm install && npm cache clean --force

COPY . .

# ENTRYPOINT [ "tini" "--" ]
ENTRYPOINT [ "/sbin/tini", "--" ]

CMD [ "node", "app.js" ]

# ----------------------------------------------------------------------------------------

# > docker buid -t multistage -- target prod . && docker run multistage
FROM node:12.16.1-alpine3.11 as prod
ENV NODE_ENV=production
COPY package*.json ./
RUN npm install --only=production && npm cache clean --force
COPY . .
CMD [ "node", "./bin/www" ]

# > docker buid -t multistage:dev --target dev . && docker run --init -p 3000:3000 multistage:dev
FROM prod as dev
ENV NODE_ENV=development
RUN npm install --only=development
CMD [ "../node_modules/nodemon/bin/nodemon.js", "./bin/www", "--inspect=0.0.0.0:992" ]

# > docker build -t multistage:test --target test . && docker run --init multistage:test
FROM dev as test
ENV NODE_ENV=development
CMD [ "npm", "test" ]
