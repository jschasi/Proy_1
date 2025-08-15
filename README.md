# Docker-Nginx Deploy on GCP
Desliegue de aplicaciones front y back con docker y nginx usando Google Cloud Platform

## Objetivo
Crear un entorno de despliegue continuo usando docker, GCP y Github Actions

## Video de Youtube
https://youtu.be/kYD8mOg5ngs

## Requisitos previos
1. Tener conocimientos de entornos linux para lo siguiente
   * Identificar y acceder al modo de super usuario
   * Navegar entre carpetas
   * Instalar aplicaciones
   * Scripts en bash
2. Conocimientos de Docker para
   * Crear Dockerfiles
   * Crear archivos de docker compose
   * Comandos para hacer correr contenedores
3. Conocimientos de Google Cloud Plataform
   * Crear máquinas virtuales
   * Permitir tráfico a través de puertos específicos
   * Conectarse por SSH
4. Github Actions
   * Crear acciones de Github
   * Crear secrets
5. Tener al menos una aplicación de frontend para desplegar
6. Tener al menos uan aplicación de backend para desplegar
  
## Pasos
1. Crear una máquina virtual desde GCP
2. Crear claves SSH para la conexión con la máquina virtual
3. Agregar las claves de conexión por GCP
4. Generar claves SSH desde el servidor para agregar a GITHUB
5. Generar Dockerfile para la aplicación de backend
6. Generar docker-compose.yml para la aplicación de backend
7. Generar Dockerfile para la aplicación de frontend
8. Generar docker-compose.yml para la aplicación de frontend
9. Generar archivo de configuración para nginx
10. Generar Dockerfile para Nginx
11. Generar docker-compose.yml para el gateway
12. Crear la red de docker en el servidor
13. Levantar todo el proyecto
14. Crear los archivo de configuración de github actions
15. Probar el despliegue continuo

## Pasos opcionales
1. Instalar certificado SSL para trabajar con puertos seguros
2. modificar el archivo host de tu computadora local.

### 1. Crear una máquina virtual desde GCP
Para crear una máquina virtual en Google Cloud Platform (GCP) que utilice Ubuntu Server, sigue estos pasos:

1. **Inicia sesión en tu cuenta de Google Cloud Platform:**
   Accede a la consola de GCP utilizando tu cuenta de Google en https://console.cloud.google.com/.

2. **Crea un proyecto (opcional):**
   Si aún no tienes un proyecto, puedes crear uno en la página de inicio de la consola de GCP. Esto ayudará a organizar tus recursos y facturación.

3. **Abre la página de "Compute Engine":**
   Desde el menú de navegación en la consola de GCP, selecciona "Compute Engine" > "Instancias de VM".

4. **Crea una instancia nueva:**
   Haz clic en el botón "Crear instancia".

5. **Configura la instancia:**
   - **Nombre de la instancia:** Asígna un nombre a tu instancia.
   - **Zona:** Selecciona la zona geográfica en la que deseas crear la instancia.
   - **Tipo de máquina:** Elige el tipo de máquina que mejor se adapte a tus necesidades.
   - **Sistema operativo:** Selecciona "Cambiar" y elige "Ubuntu". Luego, selecciona la versión de Ubuntu Server que deseas usar.

6. **Configura la identidad y el acceso:**
   - **Clave SSH:** Puedes seleccionar "Crear una clave nueva" para generar una nueva clave SSH para acceder a la instancia.
   - **Acceso a HTTPS:** Habilita el acceso a través de HTTPS si lo deseas.

7. **Configura los detalles de administración, discos, red, etc.:**
   - Puedes mantener los valores predeterminados para muchos de estos ajustes o personalizarlos según tus necesidades.

8. **Habilita las API de Google Cloud (si es necesario):**
   Si esta es la primera vez que usas GCP, es posible que debas habilitar las API de Compute Engine y otras API relacionadas.

9. **Revisa y crea la instancia:**
   Verifica la configuración de la instancia y haz clic en "Crear" para crearla.

10. **Accede a la instancia:**
    Una vez que la instancia esté creada, podrás conectarte a ella utilizando SSH. En la lista de instancias de Compute Engine, haz clic en la instancia que creaste y luego en el botón "SSH" para iniciar una sesión de terminal en la instancia.

¡Listo! Ahora tienes una máquina virtual en GCP que utiliza Ubuntu Server. Puedes instalar software, configurar servicios y realizar tareas según tus necesidades. Recuerda detener o eliminar la instancia cuando no la estés utilizando para evitar cargos innecesarios en tu cuenta de GCP.
### 2. Crear claves SSH para la coneción con la máquina virtual
Para crear claves SSH y establecer una conexión segura con un servidor Ubuntu, sigue estos pasos:

1. **Abrir una terminal local:** Si estás en un sistema operativo basado en Unix (Linux o macOS), abre la terminal. Si estás en Windows, puedes usar el emulador de terminal como Git Bash o Windows Subsystem for Linux (WSL).

2. **Generar un par de claves SSH:** Utiliza el comando `ssh-keygen` para generar un par de claves pública y privada. La clave privada se almacenará en tu sistema local, y la clave pública se copiará al servidor remoto para autenticación.

   Ejecuta el siguiente comando y sigue las instrucciones para especificar la ubicación y el nombre del archivo de clave, o simplemente presiona "Enter" para aceptar las ubicaciones predeterminadas:
   
   ```bash
   ssh-keygen -t rsa -b 4096 -C "tu_email@example.com"
   ```
   
   Sustituye `"tu_email@example.com"` con tu dirección de correo electrónico. Puedes elegir otro tipo de clave (`-t`) o longitud (`-b`) si lo prefieres.

3. **Copiar la clave pública al servidor:** Utiliza `ssh-copy-id` para copiar tu clave pública al servidor remoto. Necesitarás la contraseña de tu cuenta en el servidor para completar este paso.
   
   ```bash
   ssh-copy-id usuario@direccion_ip_del_servidor
   ```
   
   Sustituye `usuario` con tu nombre de usuario en el servidor y `direccion_ip_del_servidor` con la dirección IP o el nombre de dominio del servidor Ubuntu.

4. **Iniciar sesión con la clave SSH:** Ahora deberías poder iniciar sesión en el servidor Ubuntu sin necesidad de ingresar una contraseña. Solo necesitarás tu clave privada.

   ```bash
   ssh usuario@direccion_ip_del_servidor
   ```

Estos pasos te permitirán crear un par de claves SSH y establecer una conexión segura con tu servidor Ubuntu sin necesidad de ingresar tu contraseña cada vez. Recuerda que la seguridad es crucial, así que asegúrate de proteger adecuadamente tu clave privada y seguir las mejores prácticas de seguridad para administrar tus claves SSH.

### 3. Agregar las claves de conexión por GCP
Si has creado claves SSH a través de la consola de Google Cloud Platform (GCP) y deseas agregarlas a tus instancias de VM, aquí tienes los pasos a seguir:

1. **Accede a la consola de GCP:**
   Inicia sesión en tu cuenta de Google Cloud Platform en https://console.cloud.google.com/.

2. **Abre la página de "Compute Engine":**
   Desde el menú de navegación en la consola de GCP, selecciona "Compute Engine" > "Metadatos de SSH".

3. **Agrega tu clave SSH:**
   En la página de Metadatos de SSH, verás una sección donde puedes agregar tus claves públicas SSH. Puedes copiar y pegar la clave pública que hayas generado previamente.

4. **Asigna la clave a una instancia:**
   Una vez que hayas agregado tus claves SSH, deberás asignarlas a las instancias de VM en las que deseas permitir el acceso. Para hacerlo:

   - Abre la página de "Compute Engine" > "Instancias de VM".
   - Haz clic en la instancia a la que deseas agregar la clave SSH.
   - En la pestaña "Detalles", encontrarás la sección "Metadatos de SSH". Aquí verás una lista de las claves SSH disponibles. Puedes seleccionar una o varias claves para asignar a la instancia.

5. **Guarda los cambios:**
   Después de agregar y asignar las claves SSH a la instancia, asegúrate de guardar los cambios.

Ahora, podrás conectarte a la instancia utilizando SSH con la clave pública que has agregado a través de GCP. Ten en cuenta que solo las claves que has asignado a la instancia podrán utilizarse para acceder a ella. Esto proporciona un nivel adicional de seguridad al controlar quién puede acceder a tu instancia.

Recuerda seguir las mejores prácticas de seguridad, como proteger tus claves privadas y gestionar cuidadosamente el acceso a tus instancias de VM.
### 4. Generar claves SSH desde el servidor para agregar a GITHUB
Si deseas generar claves SSH desde tu servidor para agregarlas a tu cuenta de GitHub, sigue estos pasos:

1. **Conéctate a tu servidor:**
   Abre una terminal en tu servidor utilizando SSH. Puedes hacerlo usando el siguiente comando, reemplazando `usuario` por tu nombre de usuario y `direccion_ip_del_servidor` por la dirección IP de tu servidor:

   ```bash
   ssh usuario@direccion_ip_del_servidor
   ```

2. **Genera un par de claves SSH:**
   En la terminal de tu servidor, ejecuta el siguiente comando para generar un par de claves SSH. Puedes optar por usar una dirección de correo electrónico asociada con tu cuenta de GitHub como comentario en la clave:

   ```bash
   ssh-keygen -t rsa -b 4096 -C "tu_email@example.com"
   ```

   Esto generará un par de claves pública (`id_rsa.pub`) y privada (`id_rsa`) en el directorio `~/.ssh/`.

3. **Visualiza la clave pública:**
   Utiliza el siguiente comando para mostrar la clave pública en la terminal:

   ```bash
   cat ~/.ssh/id_rsa.pub
   ```

   Copia la salida completa de este comando, ya que es la clave pública que agregarás a GitHub.

4. **Agrega la clave SSH a GitHub:**
   Ahora, inicia sesión en tu cuenta de GitHub en https://github.com. Luego, sigue estos pasos:

   - Haz clic en tu avatar en la esquina superior derecha y selecciona "Settings".
   - En el menú de la izquierda, selecciona "SSH and GPG keys".
   - Haz clic en el botón "New SSH key".
   - Pega la clave pública que copiaste del paso anterior en el campo "Key".
   - Dale un nombre descriptivo a la clave en el campo "Title".
   - Finalmente, haz clic en "Add SSH key".

5. **Prueba la conexión SSH:**
   Puedes probar la conexión SSH con GitHub usando el siguiente comando:

   ```bash
   ssh -T git@github.com
   ```

   Si todo está configurado correctamente, recibirás un mensaje de bienvenida de GitHub.

Ahora tendrás tu clave SSH del servidor configurada para acceder a tu cuenta de GitHub. Esto facilitará la autenticación segura cuando interactúes con repositorios de GitHub desde tu servidor.

### 5. Generar Dockerfile para la aplicación de backend
Aquí tienes un ejemplo de un Dockerfile básico para tu aplicación de backend Node.js:

```Dockerfile
# Usa una imagen base de Node.js
FROM node:18

# Establece el directorio de trabajo dentro del contenedor
WORKDIR /usr/src/app

# Copia el package.json y el package-lock.json al directorio de trabajo
COPY package*.json ./

# Instala las dependencias
RUN npm install

# Copia el resto de los archivos de la aplicación
COPY . .

# Expone el puerto en el que la aplicación escucha
EXPOSE 3000

# Comando para ejecutar la aplicación
CMD [ "node", "app.js" ]
```

Asegúrate de colocar este Dockerfile en la misma carpeta que tu archivo `app.js` y otros archivos relevantes para tu aplicación de backend. Luego, puedes construir la imagen Docker usando el siguiente comando:

```bash
docker build -t nombre-de-tu-imagen .
```

Reemplaza `nombre-de-tu-imagen` con el nombre que quieras darle a tu imagen Docker. Una vez que la imagen se haya construido, puedes ejecutar un contenedor basado en ella con:

```bash
docker run -p 3000:3000 nombre-de-tu-imagen
```

Esto ejecutará tu aplicación en un contenedor Docker, exponiendo el puerto 3000 en tu máquina host para acceder a la aplicación. Puedes personalizar este Dockerfile según las necesidades de tu aplicación y el entorno.
### 6. Generar docker-compose.yml para la aplicación de backend
Aquí tienes un ejemplo de un archivo `docker-compose.yml` para tu aplicación de backend que utiliza una red personalizada llamada `demo_deploy` y el Dockerfile que has creado:

```yaml
version: '3'
services:
  backend:
    build:
      context: ./path/to/backend # Ruta al directorio donde se encuentra tu Dockerfile y la aplicación de backend
      dockerfile: Dockerfile    # Nombre de tu Dockerfile
    image: nombre-de-tu-imagen-backend
    ports:
      - "3000:3000"
    networks:
      - demo_deploy

networks:
  demo_deploy:
    driver: bridge
```

Reemplaza `/path/to/backend` con la ruta al directorio donde se encuentra tu Dockerfile y tu aplicación de backend, y `nombre-de-tu-imagen-backend` con el nombre que quieras darle a tu imagen Docker para el backend.

Asegúrate de que este archivo `docker-compose.yml` esté en el mismo directorio que tu Dockerfile y otros archivos relevantes para tu backend. Luego, puedes ejecutar el siguiente comando para iniciar tus servicios utilizando Docker Compose:

```bash
docker-compose up
```

Esto construirá tu imagen de backend utilizando el Dockerfile y ejecutará tu aplicación en un contenedor, conectándola a la red personalizada `demo_deploy`. Puedes ajustar este archivo `docker-compose.yml` según las necesidades de tu aplicación y el entorno.

### 7. Generar Dockerfile para la aplicación de frontend
Aquí tienes un ejemplo de un Dockerfile simple para servir un archivo HTML estático, como tu aplicación de frontend:

```Dockerfile
# Usa una imagen base de Nginx
FROM nginx:alpine

# Copia el archivo HTML estático a la carpeta de contenido de Nginx
COPY . /usr/share/nginx/html

# Expone el puerto 80 en el que el servidor web escucha
EXPOSE 80

# Comando por defecto para iniciar el servidor web de Nginx
CMD ["nginx", "-g", "daemon off;"]
```

En este caso, debes asegurarte de que el archivo `index.html` esté en la misma ubicación que tu Dockerfile antes de construir la imagen Docker.

Puedes construir y ejecutar esta imagen Docker de manera similar a lo que hemos visto antes:

1. Coloca tu archivo `index.html` en la misma carpeta que tu Dockerfile.
2. Abre una terminal y navega hasta la carpeta que contiene ambos archivos.
3. Ejecuta el comando para construir la imagen:

```bash
docker build -t nombre-de-tu-imagen-frontend .
```

4. Ejecuta un contenedor basado en esta imagen:

```bash
docker run -p 80:80 nombre-de-tu-imagen-frontend
```

Luego podrás acceder a tu archivo HTML estático en tu navegador visitando http://localhost:80/. Puedes personalizar este Dockerfile según tus necesidades, como añadir otros recursos estáticos o configuraciones específicas del servidor web.

### 8. Generar docker-compose.yml para la aplicación de frontend
Aquí tienes un ejemplo de un archivo `docker-compose.yml` que levanta la imagen creada a partir del Dockerfile del frontend y utiliza la red personalizada `demo_deploy`:

```yaml
version: '3'
services:
  frontend:
    build:
      context: ./path/to/frontend # Ruta al directorio donde se encuentra tu Dockerfile y la aplicación de frontend
      dockerfile: Dockerfile    # Nombre de tu Dockerfile
    image: frontend
    ports:
      - "80"
    networks:
      - demo_deploy

networks:
  demo_deploy:
    external: true
```

Reemplaza `/path/to/frontend` con la ruta al directorio donde se encuentra tu Dockerfile y tu aplicación de frontend, y `nombre-de-tu-imagen-frontend` con el nombre que le has dado a tu imagen Docker para el frontend.

Asegúrate de que este archivo `docker-compose.yml` esté en la misma carpeta que tu Dockerfile de frontend y otros archivos relevantes. Luego, puedes ejecutar el siguiente comando para iniciar tus servicios utilizando Docker Compose:

```bash
docker-compose up
```

Esto levantará tu aplicación de frontend en un contenedor Docker, conectándola a la red personalizada `demo_deploy`. Puedes ajustar este archivo `docker-compose.yml` según las necesidades de tu aplicación y el entorno.

### 9. Generar archivo de configuración para nginx
Claro, aquí tienes un ejemplo de un archivo de configuración para Nginx que maneja las ubicaciones `/api` y `/` como has descrito:

```nginx
server {
    listen 80;
    server_name tu-dominio.com; # Cambia esto a tu dominio o dirección IP

    location /api {
        proxy_pass http://backend:puerto; # Cambia "backend" por el nombre del servicio backend y "puerto" por el puerto real
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location / {
        proxy_pass http://frontend:80; # Cambia "frontend" por el nombre del servicio frontend
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Asegúrate de reemplazar `tu-dominio.com` con tu dominio o dirección IP real, y ajusta los nombres de los servicios y los puertos según tu configuración. Luego, guarda este archivo con una extensión `.conf`, por ejemplo, `nginx-config.conf`.

Cuando ejecutes tus servicios utilizando Docker Compose, puedes montar este archivo de configuración en el contenedor Nginx. Asegúrate de que el archivo de configuración esté en la misma ubicación que tu archivo `docker-compose.yml` y agrega esta sección a tu servicio Nginx en el archivo `docker-compose.yml`:

```yaml
services:
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - backend
      - frontend
```

Esto montará tu archivo de configuración en el contenedor Nginx y configurará las ubicaciones `/api` y `/` para dirigir el tráfico adecuadamente a los contenedores de backend y frontend. Recuerda ajustar las configuraciones según tu propia estructura de servicios y puertos.

### 11. Archivo de configuración del gateway
Aquí tienes un ejemplo de archivo `docker-compose.yml` que utiliza la red `demo_deploy` y configura un servicio Nginx como gateway para dirigir el tráfico a los contenedores de backend y frontend:

```yaml
version: '3'
services:
  frontend:
    extends:
      file: ./frontend/docker-compose.yml
      service: frontend
    networks:
      - demo_deploy

  backend:
    extends:
      file: ./backend/docker-compose.yml
      service: backend
    networks:
      - demo_deploy

  gateway:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    networks:
      - demo_deploy
    depends_on:
      - backend
      - frontend

networks:
  demo_deploy:
    external: true
```

Asegúrate de tener el archivo `nginx-config.conf` configurado como se describió anteriormente y ubicado en la misma carpeta que el archivo `docker-compose.yml`.

En este ejemplo, los servicios de frontend y backend se conectan a la red personalizada `demo_deploy`, y el servicio de Nginx (gateway) también se conecta a la misma red. El gateway está configurado para dirigir el tráfico a los servicios backend y frontend según las ubicaciones `/api` y `/`, respectivamente.

Al ejecutar `docker-compose up`, estos servicios se iniciarán y se comunicarán entre sí a través de la red personalizada `demo_deploy`, y el gateway Nginx dirigirá el tráfico a los contenedores correspondientes. Asegúrate de ajustar los nombres de las imágenes y las configuraciones según tu propio proyecto.

### 12. Crear la red de docker en el servidor
Puedes crear una red de Docker utilizando el siguiente comando en tu terminal:

```bash
docker network create demo_deploy
```

Esto creará una red llamada `demo_deploy` que puedes usar para conectar tus servicios de frontend, backend y gateway. Luego, cuando configures tus servicios en el archivo `docker-compose.yml`, puedes usar esta red con el nombre `demo_deploy` en la sección `networks` de cada servicio.

### 13. Levantar todo el proyecto
Para levantar todo el proyecto puedes usar el comando
```bash
docker compose up
```

### 14. Crear los archivo de configuración de github actions
Aquí tienes un ejemplo de un archivo de configuración de GitHub Actions que ejecuta un flujo cuando se realiza un push a la rama `main`. El flujo se conectará por SSH a tu servidor y realizará la reconstrucción del proyecto:

```yaml
name: SSH Command Execution

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Connect via SSH and run command
      uses: appleboy/ssh-action@master
      with:
        host: your_server_ip # Reemplaza con la dirección del servidor
        username: mi-usuario  # Reemplaza con el nombre de usuario para SSH
        key: ${{ secrets.SERVER_SSH_PRIVATE_KEY }}
        port: 22  # Puerto SSH (generalmente es 22, pero puede variar)
        script: |
          echo "Conexión SSH exitosa"
          cd /path/to/your/project && sudo sh deploy.sh # Ejecutar archivo que contiene
```

Asegúrate de reemplazar las siguientes variables:

- `username`: Tu nombre de usuario en el servidor.
- `your_server_ip`: La dirección IP de tu servidor.
- `/path/to/your/project`: La ruta en el servidor donde se encuentra tu proyecto.
- `secrets.SERVER_SSH_PRIVATE_KEY`: Debes configurar una variable secreta en tu repositorio de GitHub para almacenar la clave SSH privada que se usará para conectarse al servidor. Puedes crear una clave SSH específica para este flujo y agregar la clave privada en la configuración de secretos de tu repositorio.

Este flujo de GitHub Actions ejecutará los siguientes pasos:

1. Realizará un `checkout` del código del repositorio.
2. Configurará el agente SSH con la clave privada almacenada en el secreto.
3. Ejecutará comandos SSH para conectar al servidor, actualizar el repositorio, detener y volver a iniciar los servicios con `docker-compose`.

Asegúrate de revisar y personalizar el archivo de configuración según tus necesidades y estructura del proyecto. También, ten en cuenta las medidas de seguridad al manejar claves SSH y la conexión al servidor.

### 15. Probar el despliegue continuo
Haz un cambio en la aplicación, monitorea el despliegue y verifica los cambios en la web
