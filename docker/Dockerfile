FROM node:20-alpine AS base
WORKDIR /app

RUN apk add --no-cache openssl libc6-compat

FROM base AS deps
COPY package*.json ./
COPY prisma ./prisma/
RUN npm ci

FROM base AS builder
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN if [ -f prisma/schema.prisma ]; then npx prisma generate; fi
RUN npm run build || true

FROM base AS runner
ENV NODE_ENV=production
ENV PORT=3000

RUN addgroup --system --gid 1001 nodejs && \
    adduser --system --uid 1001 appuser

COPY --from=deps /app/node_modules ./node_modules
COPY --from=builder /app/node_modules/.prisma ./node_modules/.prisma
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/prisma ./prisma
COPY package*.json ./

USER appuser

EXPOSE 3000

CMD ["sh", "-c", "if [ -f prisma/schema.prisma ]; then npx prisma migrate deploy; fi && node dist/index.js"]