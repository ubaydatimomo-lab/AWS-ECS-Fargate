# build stage
FROM node:18-alpine AS build-stage
WORKDIR /app
COPY package.json pnpm-lock.yaml .npmrc* ./
RUN npm install -g pnpm && pnpm i --frozen-lockfile --dangerously-allow-all-builds
COPY . .
RUN pnpm build

# production stage
FROM nginx:alpine AS production-stage
RUN addgroup -g 2026 fam && adduser -D -u 2004 -G fam ubz
COPY --chown=ubz:fam --from=build-stage /app/dist /usr/share/nginx/html
COPY --chown=ubz:fam nginx.conf /etc/nginx/conf.d/default.conf
HEALTHCHECK --interval=5m --timeout=3s \
CMD curl -f http://localhost/health || exit 1
EXPOSE 80
RUN chown -R ubz:fam /var/cache/nginx /var/log/nginx /run
USER ubz
CMD ["nginx", "-g", "daemon off;"]