FROM node:22-alpine AS builder

WORKDIR /app

ARG NEXT_PUBLIC_CLIENT_BASEPATH
ARG NEXT_PUBLIC_ADMIN_API_KEY
ARG NEXT_PUBLIC_API_BASEPATH
ARG API_BASEPATH
ARG CLIENT_STATIC_BUILD_BASEPATH

ENV NEXT_PUBLIC_CLIENT_BASEPATH=${NEXT_PUBLIC_CLIENT_BASEPATH}
ENV NEXT_PUBLIC_ADMIN_API_KEY=${NEXT_PUBLIC_ADMIN_API_KEY}
ENV NEXT_PUBLIC_API_BASEPATH=${NEXT_PUBLIC_API_BASEPATH}
ENV CLIENT_STATIC_BUILD_BASEPATH=${CLIENT_STATIC_BUILD_BASEPATH}

COPY package.json package-lock.json ./
RUN npm ci

COPY . .

ENV NEXT_DISABLE_FETCH_DURING_BUILD=true

RUN npm run build

# 本番環境用イメージ
FROM node:22-alpine AS runner

WORKDIR /app

ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1

ENV NEXT_PUBLIC_CLIENT_BASEPATH=${NEXT_PUBLIC_CLIENT_BASEPATH}
ENV NEXT_PUBLIC_ADMIN_API_KEY=${NEXT_PUBLIC_ADMIN_API_KEY}
ENV NEXT_PUBLIC_API_BASEPATH=${NEXT_PUBLIC_API_BASEPATH}
ENV CLIENT_STATIC_BUILD_BASEPATH=${CLIENT_STATIC_BUILD_BASEPATH}

COPY --from=builder /app/package.json ./
COPY --from=builder /app/package-lock.json ./
COPY --from=builder /app/next-env.d.ts ./next-env.d.ts
COPY --from=builder /app/tsconfig.json ./tsconfig.json
COPY --from=builder /app/type.d.ts ./type.d.ts
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/public ./public

EXPOSE 4000
CMD ["npm", "start"]
