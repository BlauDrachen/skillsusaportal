# Stage 1: Build SCSS + copy Bootstrap JS
FROM node:20-alpine AS builder

WORKDIR /app

# Install dependencies
COPY package.json ./
RUN npm install

# Copy SCSS
COPY src/scss/ ./src/scss/

# Compile SCSS to CSS
RUN mkdir -p src/css && \
    npm run build-css -- --load-path=node_modules

# Copy Bootstrap JS bundle
RUN mkdir -p src/js && \
    cp node_modules/bootstrap/dist/js/bootstrap.bundle.min.js src/js/

# Stage 2: Lightweight Nginx
FROM nginx:alpine

# Copy built assets
COPY --from=builder /app/src/css/style.css /usr/share/nginx/html/css/style.css
COPY --from=builder /app/src/js/bootstrap.bundle.min.js /usr/share/nginx/html/js/bootstrap.bundle.min.js

# Copy HTML files
COPY src/index.html src/exercises.html /usr/share/nginx/html/

# copy custom nginx config
COPY docker/nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

HEALTHCHECK CMD wget --quiet --tries=1 http://localhost || exit 1
