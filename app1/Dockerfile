# syntax=docker/dockerfile:1

FROM node:lts-alpine
WORKDIR /app1
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
