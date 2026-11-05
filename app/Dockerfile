# build stage
FROM node:18-alpine AS build-stage
WORKDIR /app
COPY package.json pnpm-lock.yaml .npmrc* ./
RUN npm install -g pnpm && pnpm i --frozen-lockfile --dangerously-allow-all-builds
COPY . .
RUN pnpm build

# production stage
FROM nginx:alpine AS production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
HEALTHCHECK --interval=5m --timeout=3s \
CMD curl -f http://localhost/health || exit 1
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]