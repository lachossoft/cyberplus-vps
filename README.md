# cyberplus-vps

# Configuración del Proyecto con Docker Compose

Este proyecto utiliza Docker Compose para gestionar los servicios necesarios para ejecutar HESK, una herramienta de soporte técnico basada en PHP. A continuación, se detalla la configuración y el uso del archivo `docker-compose.yml`.

## Servicios Configurados

El archivo `docker-compose.yml` define los siguientes servicios:

### 1. MariaDB
- **Descripción**: Base de datos relacional utilizada por HESK.
- **Imagen**: `mariadb:latest`
- **Puertos expuestos**: `3306`
- **Variables de entorno**:
  - `MYSQL_ROOT_PASSWORD`: Contraseña del usuario root.
  - `MARAIADB_USER`: Usuario de la base de datos.
  - `MARAIADB_PASSWORD`: Contraseña del usuario de la base de datos.
  - `MARAIADB_DATABASE`: Nombre de la base de datos.
- **Volúmenes**:
  - `./mariadb_data:/var/lib/mysql`

### 2. phpMyAdmin
- **Descripción**: Interfaz gráfica para gestionar la base de datos MariaDB.
- **Imagen**: `phpmyadmin:latest`
- **Puertos expuestos**: `9090` (mapeado al puerto `90` del contenedor).
- **Variables de entorno**:
  - `PMA_ARBITRARY=1`: Permite conexiones arbitrarias.
- **Dependencias**:
  - Depende del servicio `mariadb`.

### 3. HESK
- **Descripción**: Aplicación principal de HESK.
- **Imagen**: `php:8.3.20-fpm-alpine3.20`
- **Puertos expuestos**: `80`
- **Volúmenes**:
  - `./hesk:/var/www/html`
- **Dependencias**:
  - Depende del servicio `mariadb`.

### 4. Nginx Proxy Manager
- **Descripción**: Gestor de proxy inverso para manejar las solicitudes HTTP y HTTPS.
- **Imagen**: `jc21/nginx-proxy-manager:latest`
- **Puertos expuestos**:
  - `80`: HTTP
  - `443`: HTTPS
  - `81`: Interfaz de gestión
- **Variables de entorno**:
  - `DB_MYSQL_HOST`: Host de la base de datos (mariadb).
  - `DB_MYSQL_PORT`: Puerto de la base de datos (3306).
  - `DB_MYSQL_USER`: Usuario de la base de datos.
  - `DB_MYSQL_PASSWORD`: Contraseña del usuario de la base de datos.
  - `DB_MYSQL_NAME`: Nombre de la base de datos.
- **Volúmenes**:
  - `./nginx-proxy-manager/data:/data`
  - `./nginx-proxy-manager/letsencrypt:/etc/letsencrypt`

## Redes

Se utiliza una red personalizada llamada `hesk_network` con el driver `bridge` para conectar todos los servicios.

## Volúmenes

Los siguientes volúmenes se configuran para persistir los datos:
- `mariadb_data`: Almacena los datos de MariaDB.
- `hesk_data`: Almacena los datos de HESK.
- `npm_data`: Almacena los datos de configuración de Nginx Proxy Manager.
- `npm_letsencrypt`: Almacena los certificados SSL generados por Let's Encrypt.

## Uso

### 1. Iniciar los Servicios
Ejecuta el siguiente comando para iniciar los servicios:

```bash
docker-compose up -d
```

### 2. Detener los Servicios
Para detener los servicios, ejecuta:

```bash
docker-compose down
```

### 3. Acceso a los Servicios
- **HESK**: Disponible en `http://localhost` (puerto 80).
- **phpMyAdmin**: Disponible en `http://localhost:9090`.
- **Nginx Proxy Manager**: Interfaz de gestión disponible en `http://localhost:81`.

## Notas

- Asegúrate de configurar correctamente las variables de entorno en el archivo `docker-compose.yml` o en un archivo `.env`.
- Los puertos configurados deben estar libres en tu máquina local para evitar conflictos.
