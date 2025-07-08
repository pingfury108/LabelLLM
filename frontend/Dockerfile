FROM node:20.17.0-alpine AS builder

WORKDIR /app

# Non-sensitive build arguments
ARG VITE_APP_VERSION

# install pnpm
RUN npm install -g pnpm

COPY package.json pnpm-lock.yaml* ./

# install dependencies
RUN pnpm install --frozen-lockfile

COPY . .

# build app
RUN npm run build:js

FROM nginx:1.21-alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY --from=builder /app/nginx.conf /etc/nginx/conf.d/default.conf
