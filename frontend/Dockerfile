# Usa una imagen base de Nginx
FROM nginx:alpine

# Copia el archivo HTML estático a la carpeta de contenido de Nginx
COPY . /usr/share/nginx/html

# Expone el puerto 80 en el que el servidor web escucha
EXPOSE 80

# Comando por defecto para iniciar el servidor web de Nginx
CMD ["nginx", "-g", "daemon off;"]
