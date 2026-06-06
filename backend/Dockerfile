# ============ ETAPA 1: BUILD ============
FROM node:20-alpine AS build
WORKDIR /app

COPY package*.json ./

# Instalar dependencias (incluyendo dev)
RUN npm install

# Copiar código
COPY . .

# ============ ETAPA 2: RUNTIME ============
FROM node:20-alpine

WORKDIR /app

# Crear usuario no-root
RUN addgroup -g 1001 -S nodeuser && adduser -S -D -H -u 1001 -h /home/nodeuser -s /sbin/nologin -G nodeuser nodeuser

# Copiar node_modules e código desde BUILD
COPY --from=build /app/node_modules ./node_modules
COPY --from=build /app . .

# Dar permisos correctos
RUN chown -R nodeuser:nodeuser /app

# Cambiar a usuario no-root
USER nodeuser

EXPOSE 3001

CMD ["npm", "start"]