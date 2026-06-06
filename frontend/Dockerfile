# ============ ETAPA 1: BUILD ============
FROM node:20-alpine AS build
WORKDIR /app

COPY app.js ./
COPY index.html ./


# ============ ETAPA 2: RUNTIME ============
FROM nginx:alpine

# Crear usuario no-root 
RUN addgroup -g 1001 -S nginx_user && adduser -S -D -H -u 1001 -h /var/cache/nginx -s /sbin/nologin -G nginx_user -g nginx_user nginx_user

# Limpiar contenido por defecto
RUN rm -rf /usr/share/nginx/html/*

# Copiar archivos estáticos desde la etapa BUILD
COPY --from=build /app/app.js /usr/share/nginx/html/
COPY --from=build /app/index.html /usr/share/nginx/html/

# Copiar configuración Nginx
COPY default.conf /etc/nginx/conf.d/default.conf

# Cambiar permisos
RUN chown -R nginx_user:nginx_user /usr/share/nginx/html && \
    chown -R nginx_user:nginx_user /var/cache/nginx


EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
