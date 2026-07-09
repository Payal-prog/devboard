# Stage 1 — build the app
FROM dhi.io/node:26-dev AS build

WORKDIR /app

COPY package*.json ./

RUN npm install --legacy-peer-deps

# Upgrade vite to fix CVE-2026-53571 and esbuild vulnerabilities
RUN npm install --save-dev vite@8.0.16

COPY . .

RUN npm run build

# Stage 2 — distroless runtime (minimal, secure)
FROM dhi.io/node:26

WORKDIR /app

# Copy only built files and production dependencies
COPY --from=build /app/dist ./dist

# Copy package files for runtime reference
COPY --from=build /app/package*.json ./

# Install only production dependencies in runtime image
RUN npm ci --only=production --legacy-peer-deps

COPY vite.preview.config.js ./vite.config.js

EXPOSE 4173

# Use exec form for proper signal handling
ENTRYPOINT ["node", "--no-warnings"]
CMD ["./node_modules/.bin/vite", "preview", "--host", "0.0.0.0", "--port", "4173"]
