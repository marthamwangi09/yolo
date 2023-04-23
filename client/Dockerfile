FROM node:16-alpine3.16 as build-stage

WORKDIR /client

COPY package*.json ./

RUN npm install --only=production && \
    npm cache clean --force && \
    rm -rf /tmp/*

COPY . .

RUN npm run build && \
    npm prune --production

FROM node:16-alpine3.16 as production-stage

WORKDIR /client

# Only copy the necessary files from the fisrt stage
COPY --from=build-stage /client/build ./build
COPY --from=build-stage /client/public ./public
COPY --from=build-stage /client/src ./src
COPY --from=build-stage /client/package*.json ./

# Set the environment variable for the app
ENV NODE_ENV=production

EXPOSE 3000

# Remove the node_modules, clears the npm cache and removes temporary files
RUN npm prune --production && \
    npm cache clean --force && \
    rm -rf /tmp/*

CMD ["npm", "start"]