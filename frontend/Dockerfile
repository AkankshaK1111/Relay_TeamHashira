FROM node:20-alpine AS base

WORKDIR /app

# Install deps
FROM base AS deps
COPY package.json package-lock.json* ./
RUN npm ci --only=production

# Build
FROM base AS builder
COPY package.json package-lock.json* ./
RUN npm ci
COPY . .

# NEXT_PUBLIC_ vars must be baked in at build time.
# Set these as environment variables in Railway's service settings —
# Railway automatically passes them as build args.
ARG NEXT_PUBLIC_API_URL
ARG NEXT_PUBLIC_GOOGLE_CLIENT_ID
ENV NEXT_PUBLIC_API_URL=$NEXT_PUBLIC_API_URL
ENV NEXT_PUBLIC_GOOGLE_CLIENT_ID=$NEXT_PUBLIC_GOOGLE_CLIENT_ID

RUN npm run build

# Production runner
FROM base AS runner
ENV NODE_ENV=production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

# Railway injects $PORT — Next.js standalone server.js respects it
ENV HOSTNAME="0.0.0.0"

CMD ["node", "server.js"]
