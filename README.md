# Vecinity Backend API

Backend completo para el sistema de gestión comunitaria de incidencias Vecinity, desarrollado con Node.js, Express y MySQL.

## 🚀 Características

- **Autenticación JWT** con roles y permisos
- **CRUD completo** para usuarios, reportes y categorías
- **Sistema de roles** (usuario, admin_operativo, admin_general, superadmin)
- **Subida de archivos** con procesamiento de imágenes
- **Búsquedas avanzadas** con filtros y geolocalización
- **Moderación de contenido** por administradores
- **Dashboard administrativo** con estadísticas
- **Validación de datos** con express-validator
- **Seguridad robusta** con helmet, rate limiting, etc.
- **Base de datos MySQL** con Sequelize ORM
- **Documentación completa** de la API

## 📋 Requisitos Previos

- Node.js (v16 o superior)
- MySQL (v8.0 o superior)
- npm o yarn

## 🛠️ Instalación

1. **Clonar el repositorio**
```bash
cd prototipo/backend
```

2. **Instalar dependencias**
```bash
npm install
```

3. **Configurar MySQL**
   - Instalar MySQL Server
   - Crear la base de datos ejecutando el script `database.sql`
   - O usar el comando: `mysql -u root -p < database.sql`

4. **Configurar variables de entorno**
```bash
cp config.env .env
```

Editar el archivo `.env` con tus configuraciones:
```env
# ========================================
# CONFIGURACIÓN DEL SERVIDOR
# ========================================
PORT=5000
NODE_ENV=development

# ========================================
# BASE DE DATOS MYSQL
# ========================================
DB_HOST=localhost
DB_PORT=3306
DB_NAME=vecinity_db
DB_USER=root
DB_PASSWORD=tu_password_mysql
DB_DIALECT=mysql

# ========================================
# JWT Y AUTENTICACIÓN
# ========================================
JWT_SECRET=tu_jwt_secret_super_seguro_aqui_cambialo_en_produccion
JWT_EXPIRE=30d
JWT_COOKIE_EXPIRE=30

# ========================================
# CONFIGURACIÓN DE SESIONES
# ========================================
SESSION_SECRET=tu_session_secret_aqui_cambialo_en_produccion

# ========================================
# CONFIGURACIÓN DE EMAIL (OPCIONAL)
# ========================================
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=tu_email@gmail.com
EMAIL_PASS=tu_password_de_aplicacion
EMAIL_FROM=noreply@vecinity.com

# ========================================
# CONFIGURACIÓN DE ARCHIVOS
# ========================================
UPLOAD_PATH=./uploads
MAX_FILE_SIZE=10485760
ALLOWED_IMAGE_TYPES=image/jpeg,image/jpg,image/png,image/webp
ALLOWED_VIDEO_TYPES=video/mp4,video/avi,video/mov,video/wmv

# ========================================
# CONFIGURACIÓN DE SEGURIDAD
# ========================================
BCRYPT_ROUNDS=12
RATE_LIMIT_WINDOW=15
RATE_LIMIT_MAX=100

# ========================================
# CONFIGURACIÓN DE CORS
# ========================================
CORS_ORIGIN=http://localhost:3000,http://localhost:5173

# ========================================
# CONFIGURACIÓN DE LOGS
# ========================================
LOG_LEVEL=combined
LOG_FILE=./logs/app.log

# ========================================
# CONFIGURACIÓN DE PAGINACIÓN
# ========================================
DEFAULT_PAGE_SIZE=20
MAX_PAGE_SIZE=100

# ========================================
# CONFIGURACIÓN DE BÚSQUEDA
# ========================================
SEARCH_RADIUS_KM=5
MAX_SEARCH_RADIUS_KM=50

# ========================================
# CONFIGURACIÓN DE NOTIFICACIONES
# ========================================
ENABLE_EMAIL_NOTIFICATIONS=true
ENABLE_WHATSAPP_NOTIFICATIONS=false

# ========================================
# CONFIGURACIÓN DE BACKUP
# ========================================
BACKUP_PATH=./backups
BACKUP_RETENTION_DAYS=30
```

5. **Iniciar el servidor**
```bash
# Desarrollo
npm run dev

# Producción
npm start
```

## 📁 Estructura del Proyecto

```
backend/
├── config/             # Configuración de base de datos
│   └── database.js     # Configuración Sequelize
├── models/             # Modelos de Sequelize
│   ├── User.js         # Modelo de usuario
│   ├── Report.js       # Modelo de reportes
│   ├── Category.js     # Modelo de categorías
│   └── index.js        # Asociaciones entre modelos
├── routes/             # Rutas de la API
│   ├── auth.js         # Autenticación
│   ├── users.js        # Gestión de usuarios
│   ├── reports.js      # Gestión de reportes
│   ├── categories.js   # Gestión de categorías
│   └── admin.js        # Panel administrativo
├── middleware/         # Middleware personalizado
│   ├── auth.js         # Autenticación y autorización
│   ├── errorHandler.js # Manejo de errores
│   └── upload.js       # Subida de archivos
├── uploads/           # Archivos subidos
├── database.sql       # Script de creación de BD
├── server.js          # Servidor principal
├── package.json       # Dependencias
└── README.md          # Documentación
```

## 🗄️ Base de Datos MySQL

### Tablas Principales

1. **users**: Información de usuarios
2. **categories**: Categorías y subcategorías
3. **reports**: Reportes de incidencias

### Características de la BD

- **Índices optimizados** para búsquedas rápidas
- **Índices espaciales** para geolocalización
- **Índices de texto completo** para búsquedas
- **Vistas** para consultas complejas
- **Procedimientos almacenados** para operaciones comunes
- **Triggers** para actualización automática de timestamps
- **Eventos programados** para mantenimiento

### Script de Creación

Ejecutar el archivo `database.sql` para crear toda la estructura:

```bash
mysql -u root -p < database.sql
```

## 🔐 Autenticación y Autorización

### Roles de Usuario

1. **Usuario común**: Puede crear, editar y eliminar sus propios reportes
2. **Admin Operativo**: Puede moderar contenido y cambiar estatus de reportes
3. **Admin General**: Puede gestionar categorías, usuarios y eliminar reportes
4. **Superadmin**: Control total del sistema

### Tokens JWT

Para acceder a rutas protegidas, incluir el token en el header:
```
Authorization: Bearer <token>
```

## 📚 Documentación de la API

### Autenticación

#### POST /api/auth/register
Registrar nuevo usuario
```json
{
  "nombre": "Juan Pérez",
  "calle": "Av. Principal",
  "numero": "123",
  "email": "juan@email.com",
  "whatsapp": "+525512345678",
  "password": "123456"
}
```

#### POST /api/auth/login
Iniciar sesión
```json
{
  "email": "juan@email.com",
  "password": "123456"
}
```

#### GET /api/auth/me
Obtener perfil del usuario actual (requiere token)

### Reportes

#### GET /api/reports
Obtener todos los reportes públicos
```
Query params:
- page: número de página
- limit: elementos por página
- categoria: ID de categoría
- estatus: nuevo|en_proceso|resuelto|cerrado
- lat, lng, radio: búsqueda por ubicación
- search: búsqueda por texto
```

#### POST /api/reports
Crear nuevo reporte (requiere token)
```json
{
  "titulo": "Coladera abierta",
  "descripcion": "Hay una coladera abierta en la esquina",
  "direccion": "Av. Principal 123",
  "latitud": 19.4326,
  "longitud": -99.1332,
  "categoria_id": 1,
  "subcategoria": "Coladeras",
  "prioridad": "media",
  "folio": "FOL-2024-001"
}
```

#### PUT /api/reports/:id/status
Cambiar estatus del reporte (requiere token)
```json
{
  "estatus": "en_proceso",
  "comentario": "Se está trabajando en la solución"
}
```

### Categorías

#### GET /api/categories
Obtener todas las categorías activas

#### POST /api/categories
Crear nueva categoría (requiere admin)
```json
{
  "nombre": "Infraestructura",
  "descripcion": "Problemas de infraestructura urbana",
  "color": "#3B82F6",
  "subcategorias": [
    {
      "nombre": "Coladeras",
      "descripcion": "Problemas con coladeras"
    }
  ]
}
```

### Usuarios

#### GET /api/users/profile
Obtener perfil del usuario (requiere token)

#### PUT /api/users/profile
Actualizar perfil (requiere token)
```json
{
  "nombre": "Juan Pérez Actualizado",
  "preferences": {
    "notifications": {
      "email": true,
      "whatsapp": false
    }
  }
}
```

### Administración

#### GET /api/admin/dashboard
Dashboard administrativo (requiere admin)

#### PUT /api/admin/reports/:id/moderate
Moderar reporte (requiere admin)
```json
{
  "isModerado": true,
  "motivoModeracion": "Contenido inapropiado"
}
```

## 🔧 Configuración de Desarrollo

### Variables de Entorno

| Variable | Descripción | Valor por Defecto |
|----------|-------------|-------------------|
| `PORT` | Puerto del servidor | 5000 |
| `NODE_ENV` | Ambiente de ejecución | development |
| `DB_HOST` | Host de MySQL | localhost |
| `DB_PORT` | Puerto de MySQL | 3306 |
| `DB_NAME` | Nombre de la base de datos | vecinity_db |
| `DB_USER` | Usuario de MySQL | root |
| `DB_PASSWORD` | Contraseña de MySQL | - |
| `JWT_SECRET` | Clave secreta para JWT | - |
| `JWT_EXPIRE` | Expiración del token JWT | 30d |
| `UPLOAD_PATH` | Ruta para archivos subidos | ./uploads |
| `MAX_FILE_SIZE` | Tamaño máximo de archivo | 10485760 (10MB) |

### Scripts Disponibles

```bash
npm run dev      # Iniciar en modo desarrollo con nodemon
npm start        # Iniciar en modo producción
npm test         # Ejecutar pruebas (pendiente)
```

## 🛡️ Seguridad

- **Helmet**: Headers de seguridad
- **Rate Limiting**: Límite de requests por IP
- **CORS**: Configuración de orígenes permitidos
- **XSS Protection**: Protección contra XSS
- **Input Validation**: Validación de entrada con express-validator
- **Password Hashing**: Encriptación con bcryptjs
- **SQL Injection Protection**: Sequelize ORM con prepared statements

## 📊 Base de Datos

### Características Avanzadas

- **Búsquedas espaciales** con índices geográficos
- **Búsquedas de texto completo** con FULLTEXT
- **JSON fields** para datos flexibles
- **Vistas materializadas** para consultas complejas
- **Procedimientos almacenados** para operaciones optimizadas
- **Triggers** para integridad referencial
- **Eventos programados** para mantenimiento automático

### Optimización

- Índices compuestos para consultas frecuentes
- Particionamiento por fecha (opcional)
- Configuración de buffer pool optimizada
- Consultas optimizadas con EXPLAIN

## 🚀 Despliegue

### Producción

1. **Configurar variables de entorno de producción**
2. **Configurar MySQL Server optimizado**
3. **Configurar servidor web (nginx/apache)**
4. **Configurar SSL/HTTPS**
5. **Configurar logs y monitoreo**
6. **Configurar backup automático**

### Docker (Opcional)

```dockerfile
FROM node:16-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 5000
CMD ["npm", "start"]
```

### Docker Compose

```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "5000:5000"
    environment:
      - NODE_ENV=production
      - DB_HOST=mysql
    depends_on:
      - mysql
    volumes:
      - ./uploads:/app/uploads

  mysql:
    image: mysql:8.0
    environment:
      - MYSQL_ROOT_PASSWORD=rootpassword
      - MYSQL_DATABASE=vecinity_db
    volumes:
      - mysql_data:/var/lib/mysql
      - ./database.sql:/docker-entrypoint-initdb.d/init.sql

volumes:
  mysql_data:
```

## 🤝 Contribución

1. Fork el proyecto
2. Crear rama para feature (`git checkout -b feature/AmazingFeature`)
3. Commit cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abrir Pull Request

## 📝 Licencia

Este proyecto está bajo la Licencia ISC.

## 🆘 Soporte

Para soporte técnico o preguntas:
- Crear un issue en el repositorio
- Contactar al equipo de desarrollo

## 🔄 Changelog

### v1.0.0
- Migración de MongoDB a MySQL
- Implementación de Sequelize ORM
- Sistema de autenticación JWT
- CRUD completo para todas las entidades
- Sistema de roles y permisos
- Subida y procesamiento de archivos
- API REST completa
- Documentación detallada
- Scripts SQL para configuración automática 