FROM node:22

WORKDIR /usr/src/app

COPY package.json package-lock.json ./
RUN npm ci --production
COPY . .
RUN npm run build
