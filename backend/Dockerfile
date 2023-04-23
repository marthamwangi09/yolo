FROM node:16-alpine3.16

WORKDIR /backend

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5000

# Start the application
CMD ["npm", "start"]