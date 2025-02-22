# Usar una imagen base de Node.js con npm preinstalado
FROM node:16-alpine

# Establecer el directorio de trabajo dentro del contenedor
WORKDIR /app

# Copiar los archivos de dependencias
COPY package.json package-lock.json ./

# Instalar las dependencias del proyecto
RUN npm install

# Copiar el resto del código fuente del frontend
COPY . .

# Ejecutar el build del frontend
RUN npm run build

# Exponer el puerto donde se sirve la aplicación (si es necesario)
EXPOSE 3000

# Comando por defecto para ejecutar el contenedor
CMD ["npm", "start"]

