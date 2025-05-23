# cyberplus-vps

# Configuración del Proyecto con Docker Compose

Este proyecto utiliza Docker Compose para gestionar los servicios necesarios para ejecutar HESK, una herramienta de soporte técnico basada en PHP. A continuación, se detalla la configuración y el uso del archivo `docker-compose.yml`.

## Servicios Configurados

El archivo `docker-compose.yml` define los siguientes servicios:

### 1. PostgresDB
- **Descripción**: Base de datos PostgreSQL utilizada por el gestor de proxy inverso.
- **Imagen**: `postgres:latest`
- **Puertos expuestos**: `5432`
- **Variables de entorno**:
  - `POSTGRES_USER`: Usuario de la base de datos.
  - `POSTGRES_PASSWORD`: Contraseña del usuario de la base de datos.
  - `POSTGRES_DB`: Nombre de la base de datos.
- **Volúmenes**:
  - `./postgres_data:/var/lib/postgresql/data`

### 2. Proxy (Nginx Proxy Manager)
- **Descripción**: Gestor de proxy inverso para manejar las solicitudes HTTP y HTTPS.
- **Imagen**: `jc21/nginx-proxy-manager:latest`
- **Puertos expuestos**:
  - `80`: HTTP
  - `443`: HTTPS
- **Variables de entorno**:
  - `DB_POSTGRES_HOST`: Host de la base de datos PostgreSQL.
  - `DB_POSTGRES_PORT`: Puerto de la base de datos PostgreSQL.
  - `DB_POSTGRES_USER`: Usuario de la base de datos PostgreSQL.
  - `DB_POSTGRES_PASSWORD`: Contraseña del usuario de la base de datos PostgreSQL.
  - `DB_POSTGRES_NAME`: Nombre de la base de datos PostgreSQL.
- **Volúmenes**:
  - `./npm_data:/data`
  - `./npm_letsencrypt:/etc/letsencrypt`

### 3. MariaDB
- **Descripción**: Base de datos relacional utilizada por HESK.
- **Imagen**: `mariadb:latest`
- **Puertos expuestos**: `3306`
- **Variables de entorno**:
  - `MARIADB_ROOT_PASSWORD`: Contraseña del usuario root.
- **Volúmenes**:
  - `./mariadb_data:/var/lib/mysql`

### 4. phpMyAdmin
- **Descripción**: Interfaz gráfica para gestionar la base de datos MariaDB.
- **Imagen**: `phpmyadmin:latest`
- **Puertos expuestos**: `80`
- **Variables de entorno**:
  - `PMA_HOST`: Host de la base de datos MariaDB.
  - `PMA_PORT`: Puerto de la base de datos MariaDB.
  - `PMA_ARBITRARY`: Permite conexiones arbitrarias.
- **Dependencias**:
  - Depende del servicio `mariadb`.

### 5. HESK
- **Descripción**: Aplicación principal de HESK.
- **Imagen**: `brunoah/hesk`
- **Puertos expuestos**: `80`
- **Volúmenes**:
  - `./hesk:/var/www/html`
- **Dependencias**:
  - Depende del servicio `mariadb`.

## Redes

Se utiliza una red personalizada llamada `npm_network` con el driver `bridge` para conectar todos los servicios.

## Volúmenes

Los siguientes volúmenes se configuran para persistir los datos:
- `postgres_data`: Almacena los datos de PostgreSQL.
- `npm_data`: Almacena los datos de configuración de Nginx Proxy Manager.
- `npm_letsencrypt`: Almacena los certificados SSL generados por Let's Encrypt.
- `mariadb_data`: Almacena los datos de MariaDB.
- `hesk`: Almacena los datos de HESK.

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
- **phpMyAdmin**: Disponible en `http://localhost:80`.
- **Nginx Proxy Manager**: Interfaz de gestión disponible en `http://localhost:81`.

## Notas

- Asegúrate de configurar correctamente las variables de entorno en el archivo `docker-compose.yml` o en un archivo `.env`.
- Los puertos configurados deben estar libres en tu máquina local para evitar conflictos.
