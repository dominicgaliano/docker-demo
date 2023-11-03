# syntax=docker/dockerfile:1

FROM node:lts-alpine
WORKDIR /app2
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3001
CMD ["node", "index.js"]
