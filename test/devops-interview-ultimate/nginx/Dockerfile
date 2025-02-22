FROM nginx:alpine

# Copiar la configuración principal (si la necesitas)
COPY nginx.conf /etc/nginx/nginx.conf

# Copiar la configuración del servidor virtual
COPY conf.d/default.conf /etc/nginx/conf.d/default.conf

# No necesitas el COPY de frontend, ya que eso lo maneja el volumen en docker-compose

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

