FROM node:20.17.0-alpine AS builder

WORKDIR /app

# Non-sensitive build arguments
ARG VITE_APP_VERSION

COPY package.json package-lock.json* ./

# install dependencies
RUN npm ci || npm install

COPY . .

# build app
RUN npm run build:js

FROM nginx:1.21-alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY --from=builder /app/nginx.conf /etc/nginx/conf.d/default.conf
