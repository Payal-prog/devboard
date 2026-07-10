# Stage 1 — build the app
FROM dhi.io/node:26-dev AS build

WORKDIR /app

COPY package*.json ./

RUN npm install --legacy-peer-deps && \
    npm install --save-dev vite@8.0.16 && \
    echo "Dependencies installed"

COPY . .

RUN npm run build

# Stage 2 — distroless runtime (minimal, secure)
FROM dhi.io/node:26

WORKDIR /app

# Copy only built files and production dependencies
COPY --from=build /app/dist ./dist
COPY --from=build /app/node_modules ./node_modules
COPY --from=build /app/package*.json ./

EXPOSE 4173

# Docker appends the arguments in CMD to the arguments in ENTRYPOINT. Your configuration executes exactly like this:
# node --no-warnings ./node_modules/.bin/vite preview --host 0.0.0.0 --port 4173

ENTRYPOINT ["node", "--no-warnings"]
CMD ["./node_modules/.bin/vite", "preview", "--host", "0.0.0.0", "--port", "4173"]


# 1. The Core Runtime (node --no-warnings)node: Launches the Node.js JavaScript runtime environment.
# --no-warnings: Hides Node.js process warnings (like deprecation alerts) from the terminal logs to keep your console output clean.
#
# 2. The Execution Target (./node_modules/.bin/vite)
# This points to the local Vite executable installed inside your project's dependencies.
# It acts as the build tool and development server engine for your application.
# 
# 3. The Action (preview)preview: Tells Vite to spin up a local static web server to host your pre-built production files (usually located in the dist folder).
# Note: This does not compile your code; you must run vite build before executing this command.
# 
# 4. The Network Configurations (--host and --port)--host 0.0.0.0: Listens on all available network interfaces. 
# This is mandatory for Docker; without it, the server only listens inside the container (localhost), blocking outside access.
# --port 4173: Forces the server to run specifically on port 4173, which is Vite's default preview port.