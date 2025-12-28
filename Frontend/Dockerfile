# ---------- Build stage ----------
FROM node:18 AS builder

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

# ---------- Runtime stage ----------
FROM nginx:alpine

# 🔴 REMOVE default nginx config (important)
RUN rm /etc/nginx/conf.d/default.conf

# ✅ COPY your SPA nginx config
COPY nginx.conf /etc/nginx/conf.d/default.conf

# ✅ COPY React build output
COPY --from=builder /app/build /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
