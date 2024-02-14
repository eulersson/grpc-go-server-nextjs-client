# Adapted from https://github.com/vercel/next.js/blob/56d35ede8ed2ab25fa8e29583d4e81e3e76a0e29/examples/with-docker/Dockerfile

FROM node:20.11.0-alpine as base

FROM base AS deps
RUN apk add --update --no-cache libc6-compat protoc
WORKDIR /app
COPY client/package.json client/package-lock.json ./
RUN npm ci
COPY taconez_grpc.proto .
RUN mkdir -p ./lib/rpc protoc \
 && protoc \
      --plugin=./node_modules/.bin/protoc-gen-ts_proto \
      --ts_proto_out=./lib/rpc \
      --ts_proto_opt=outputServices=grpc-js \
      ./taconez_grpc.proto

FROM base as development
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
EXPOSE 3000
ENV PORT 3000
ENV HOSTNAME "0.0.0.0"
CMD npm run dev

FROM base as builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY client .
RUN npm run build

FROM base as production
ENV NEXT_TELEMETRY_DISABLED 1
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs
COPY --from=builder /app/public ./public
RUN mkdir .next
RUN chown nextjs:nodejs .next
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static
USER nextjs
EXPOSE 3000
ENV PORT 3000
ENV HOSTNAME "0.0.0.0"
CMD ["node", "server.js"]