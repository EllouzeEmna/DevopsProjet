# Stage 1: Build
FROM node:12.7-alpine AS build
WORKDIR /usr/src/app

# Copier les fichiers package et installer les dépendances
COPY package.json package-lock.json ./ 
RUN npm install

# Copier les fichiers du projet et effectuer la compilation
COPY . .
RUN npm run build

# Stage 2: Serve with Nginx
FROM nginx:1.17.1-alpine

# Copier la configuration Nginx
COPY nginx.conf /etc/nginx/nginx.conf

# Copier les fichiers construits depuis la phase de build
COPY --from=build /usr/src/app/dist/angular-frontend /usr/share/nginx/html

# Exposer le port 80
EXPOSE 80

# Lancer Nginx
CMD ["nginx", "-g", "daemon off;"]
