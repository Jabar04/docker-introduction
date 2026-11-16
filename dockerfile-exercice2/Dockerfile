# --- ÉTAPE 1 : Build (Construction de l'application) ---
# Tag précis (version exacte de Node et d'Alpine)
FROM node:22.23.5-alpine3.24 AS builder

WORKDIR /app

# Privilégier COPY plutôt que ADD (évite l'extraction automatique d'archives ou le téléchargement d'URL)
COPY package*.json ./

# Installation propre des dépendances
RUN npm ci

COPY . .

# Compilation du projet Vite (génère le dossier /app/dist)
RUN npm run build


# --- ÉTAPE 2 : Production (Image minimale et non-root) ---
# Image officielle Nginx préconfigurée pour tourner sans droits root (utilisateur 101)
FROM nginxinc/nginx-unprivileged:1.27-alpine3.20

# Copie uniquement du résultat compilé depuis l'étape 1 (Node et node_modules sont abandonnés)
COPY --from=builder /app/dist /usr/share/nginx/html

# L'image nginx-unprivileged écoute par défaut sur le port 8080 (les ports < 1024 nécessitant root)
EXPOSE 8080

# Passage explicite sur l'utilisateur non-privilégié (UID 101 = nginx)
USER 101

CMD ["nginx", "-g", "daemon off;"]