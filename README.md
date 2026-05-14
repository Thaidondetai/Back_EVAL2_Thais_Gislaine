# Backend - API REST con Node.js y Express

## Descripción
Backend API desarrollado en JavaScript con Node.js y Express framework. Proporciona endpoints RESTful para la gestión de usuarios con conexión a base de datos MySQL.

## Versiones y Herramientas Requeridas

### Lenguajes y Runtime
- **Node.js**: Versión 18.0.0 o superior
- **npm**: Versión 8.0.0 o superior (incluido con Node.js)

### Dependencias Principales
- **express**: ^4.18.2 - Framework web para Node.js
- **cors**: ^2.8.5 - Middleware para habilitar CORS
- **mysql2**: ^3.6.0 - Driver de MySQL para Node.js
- **dotenv**: ^16.3.1 - Manejo de variables de entorno

### Dependencias de Desarrollo
- **nodemon**: ^3.0.1 - Para desarrollo con recarga automática

## Instalación

```bash
# Instalar dependencias
npm install

# Instalar dependencias de desarrollo
npm install --save-dev nodemon
```

## Configuración

1. Copiar el archivo de variables de entorno:
```bash
cp .env.example .env
```

2. Editar el archivo `.env` con las credenciales de tu base de datos MySQL:
```
PORT=3000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=tu_contraseña
DB_NAME=proyecto_db
DB_PORT=3306
```

## Ejecución

```bash
# Para producción
npm start

# Para desarrollo (con recarga automática)
npm run dev
```

## Endpoints de la API

### Usuarios
- `GET /api/usuarios` - Obtener todos los usuarios
- `POST /api/usuarios` - Crear un nuevo usuario
- `PUT /api/usuarios/:id` - Actualizar un usuario existente
- `DELETE /api/usuarios/:id` - Eliminar un usuario

### Ejemplo de uso
```bash
# Obtener todos los usuarios
curl http://localhost:3000/api/usuarios

# Crear un nuevo usuario
curl -X POST http://localhost:3000/api/usuarios \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Juan Pérez","email":"juan@example.com","edad":25}'
```

## Puertos Requeridos

### Para funcionamiento en contenedor:
- **Puerto 3000**: Puerto del servidor backend (HTTP)
- **Puerto 3306**: Puerto de conexión a base de datos MySQL (externo)

### Explicación de puertos:
- **3000**: Es el puerto donde escucha el servidor Express para recibir peticiones HTTP
- **3306**: Es el puerto estándar para comunicación con el servidor MySQL

## Estructura del Proyecto
```
backend/
├── server.js          # Archivo principal del servidor
├── package.json       # Configuración de dependencias
├── .env.example       # Ejemplo de variables de entorno
├── .env              # Variables de entorno (crear manualmente)
└── README.md         # Este archivo
```

## Notas Importantes
- Asegúrate de tener MySQL instalado y corriendo antes de iniciar el backend
- La base de datos `proyecto_db` debe existir (ver proyecto `database/`)
- El servidor se reiniciará automáticamente en modo desarrollo si usas `npm run dev`

## Contenerización con Docker
El backend está preparado para ejecutarse dentro de un contenedor Docker.

**Construccion de la imagen**:
|docker build -t backend .|
**Ejecución del Contenedor**
|docker run -d -p 3000:3000 \
  -e DB_HOST=IP_PRIVADA_DB \
  -e DB_USER=root \
  -e DB_PASSWORD=root \
  -e DB_NAME=proyecto_db \
  backend|

## Orquestación con Docker Compose
En entornos de desarrollo se utiliza Docker Compose para levantar todos los servicios como :

- Backend (Node.js)
- Base de datos MySQL
- Frontend (Flask)

Se utiliza el comando : |docker-compose up|

Se comunican en este entorno mediante los nombres de los contenedores.

## Persistencia de Datos
Se utiliza un named volume en docker para la base de datos:
|volumes:
  mysql_data:|

Permitiendo :
- Mantener los datos aunque el contenedor se reinicie
- Evitar pérdida de información crítica
- Asegurar continuidad operativa

## CI/CD con GitHub Actions

- El proyecto se le implemento un pipeline de integración y despliegue continuo.
- El pipeline se ejecuta automaticamente al hacer push en la rama **deploy**

**Etapas del pipeline**
1. Instalación de dependencias Node.js
2. Construcción de la imagen Docker
3. Publicación en Docker Hub
4. Despliegue automático en la instancia EC2

## Despliegue en Subred Privada
El backend se despliega en una instancia EC2 ubicada en una subred privada lo que significa que:

- No es accesible directamente desde internet
- Solo recibe tráfico desde el frotend
- Se conecta a la base de datos mediante IP privada

## Despliegue mediante Bastion Host
Como el backend esta alojado en una subred privada ,el despliegue se realiza meidante un Bastion Host.

El pipeline se Conecta primero a la EC2 pública ,desde ahí accede a la EC2 privada y ejecuta el despliegue del contenedor.

## Conexion con Base de Datos
En producción el backend se conecta a la base de datos (Data) mediante la IP privada:
|DB_HOST=IP_PRIVADA_DB|
Permite:
- Comunicación segura dentro de la VPC
- Evitar exposición de la base de datos a internet

## Integración con Frontend
El backend recibe peticiones desde el frontend desplegado en la subred pública siguiendo este flujo:

1. Usuario accede al frontend
2. El Frontend (Flask) realiza peticiones HTTP al backend
3. El Backend procesa la solicitud y consulta la base de datos
4. Retorna la respuesta al Frontend
 
## Seguridad
Se configuraron reglas de seguridad(Security Groups)para:

- Permitir acceso al backend solo desde el frontend
- Permitir acceso a la base de datos solo desde el backend
- Bloquear acceso externo directo a servicios internos




