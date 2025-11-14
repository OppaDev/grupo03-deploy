

## 📋 Requisitos Previos

- Docker Desktop instalado y en ejecución
- Git
- Puerto 8080 disponible (o modificar `NGINX_PORT` en `.env`)

## 🚀 Instalación y Configuración

### 1. Clonar el Repositorio

```bash
git clone https://github.com/OppaDev/grupo03-deploy.git
cd grupo03-deploy
```

### 2. Configurar Variables de Entorno

El archivo `.env` ya está incluido con valores por defecto. **Edítalo según tus necesidades:**

```bash
# Editar archivo .env
notepad .env
```

**Configuraciones importantes a revisar:**

- **Base de Datos:**
  - `DB_PASSWORD`: Cambiar por una contraseña segura en producción
  
- **Correo Electrónico:**
  - `MAIL_HOST`, `MAIL_USERNAME`, `MAIL_PASSWORD`: Configurar tu servidor SMTP

- **Aplicación:**
  - `APP_URL`: Tu dominio o URL pública
  - `APP_ENV`: `production` para producción, `local` para desarrollo

### 3. Iniciar los Contenedores

```bash
# Construir e iniciar todos los servicios
docker compose up -d
```

Este comando:
- Descarga las imágenes necesarias
- Crea la base de datos PostgreSQL
- Despliega la aplicación Laravel
- Configura el servidor Nginx
- Ejecuta migraciones y seeders automáticamente

### 4. Verificar el Estado

```bash
# Ver logs en tiempo real
docker compose logs -f

# Ver estado de los contenedores
docker compose ps

# Ver logs de un servicio específico
docker compose logs app
docker compose logs postgres
docker compose logs nginx
```

### 5. Acceder a la Aplicación

Abre tu navegador y ve a:

```
http://localhost:8080
```

Si cambiaste el puerto en `.env`, usa el puerto configurado.

## 🛠️ Comandos Útiles

### Gestión de Contenedores

```bash
# Detener todos los servicios
docker compose stop

# Iniciar servicios detenidos
docker compose start

# Reiniciar todos los servicios
docker compose restart

# Detener y eliminar contenedores (mantiene los datos)
docker compose down

# Detener y eliminar TODO (contenedores + volúmenes + datos)
docker compose down -v
```

### Ver Logs

```bash
# Logs de todos los servicios
docker compose logs -f

# Logs solo de la aplicación
docker compose logs -f app

# Ver últimas 100 líneas
docker compose logs --tail=100
```

### Ejecutar Comandos en la Aplicación

```bash
# Abrir shell en el contenedor de la aplicación
docker compose exec app sh

# Ejecutar artisan commands
docker compose exec app php artisan migrate
docker compose exec app php artisan cache:clear
docker compose exec app php artisan config:cache

# Ver rutas
docker compose exec app php artisan route:list
```

### Actualizar la Aplicación

```bash
# Descargar nueva versión de la imagen
docker compose pull app

# Recrear solo el contenedor de la app
docker compose up -d --no-deps app

# O actualizar todo
docker compose down
docker compose pull
docker compose up -d
```

## Servicios Docker

### PostgreSQL (`postgres`)
- **Puerto:** 5432 (configurable con `DB_PORT`)
- **Volumen:** `postgres_data` (persistente)
- **Health check:** Activo

### Laravel App (`app`)
- **Imagen:** `oppadev/inferno-club:latest`
- **Volúmenes:** storage, logs, facturas
- **Migraciones:** Automáticas al iniciar

### Nginx (`nginx`)
- **Puerto:** 8080 (configurable con `NGINX_PORT`)
- **Health check:** Endpoint `/health`

## 🔧 Solución de Problemas

### El contenedor no inicia

```bash
# Ver logs detallados
docker compose logs

# Verificar que los puertos no estén en uso
netstat -ano | findstr :8080
netstat -ano | findstr :5432
```

### Error de permisos

```bash
# Recrear volúmenes
docker compose down -v
docker compose up -d
```

### Base de datos no conecta

```bash
# Verificar que PostgreSQL esté saludable
docker compose ps

# Ver logs de PostgreSQL
docker compose logs postgres

# Reiniciar servicio de base de datos
docker compose restart postgres
```

### La aplicación muestra error 500

```bash
# Ver logs de la aplicación
docker compose logs app

# Limpiar caché
docker compose exec app php artisan cache:clear
docker compose exec app php artisan config:clear
```
