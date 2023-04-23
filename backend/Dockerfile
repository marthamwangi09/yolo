FROM node:16-alpine3.16

WORKDIR /backend

COPY package*.json ./

RUN npm install --only=production && \
    npm cache clean --force && \
    rm -rf /tmp/*

COPY . .

# Set the environment variable for the backend
ENV NODE_ENV=production

EXPOSE 5000

# Remove the node_modules, clears the npm cache and removes temporary files
RUN npm prune --production && \
    npm cache clean --force && \
    rm -rf /tmp/*

# Start the application
CMD ["npm", "start"]