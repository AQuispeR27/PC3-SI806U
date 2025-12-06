# ============================================
# Dockerfile para Frontend - Sistema Culqui
# Multi-stage build para optimizar tamaño
# ============================================

# ============================================
# Etapa 1: Build
# ============================================
FROM node:18-alpine AS builder

# Metadata
LABEL maintainer="culqui-team"
LABEL description="Frontend React del sistema de login Culqui"

# Directorio de trabajo
WORKDIR /app

# Copiar archivos de dependencias
COPY package*.json ./

# Instalar dependencias
RUN npm ci

# Copiar código fuente
COPY . .

# Argumento para la URL del API (configurable en build time)
ARG REACT_APP_API_URL=http://localhost:5000/api
ENV REACT_APP_API_URL=$REACT_APP_API_URL

# Build de la aplicación React
RUN npm run build

# ============================================
# Etapa 2: Production con NGINX
# ============================================
FROM nginx:1.25-alpine

# Instalar curl para health checks
RUN apk add --no-cache curl

# Copiar archivos build desde la etapa anterior
COPY --from=builder /app/build /usr/share/nginx/html

# Copiar configuración personalizada de NGINX
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Exponer puerto
EXPOSE 80

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
    CMD curl -f http://localhost/health || exit 1

# El comando por defecto de nginx ya está en la imagen base
# Ejecutar NGINX en foreground
CMD ["nginx", "-g", "daemon off;"]
