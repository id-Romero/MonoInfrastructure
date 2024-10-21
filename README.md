# Examen Despliegue de Infraestructura con Terraform

Este proyecto tiene como objetivo desplegar una infraestructura automatizada utilizando Terraform, Docker, y GitHub Actions. Se crea una máquina virtual que ejecuta múltiples contenedores con Docker, gestionada mediante un proxy inverso Nginx, un certificado SSL emitido por Let's Encrypt, y la API MonoMap con MongoDB como base de datos.

## Estructura del Proyecto

- **`.github/workflows/`**: Contiene las configuraciones de GitHub Actions.
  - **`deploy-dev.yml`**: Despliega la infraestructura al hacer push en la rama `main`.
  - **`destroy-dev.yml`**: Destruye la infraestructura manualmente usando `workflow_dispatch`.

- **`env/dev/containers/`**: Contiene el archivo `docker-compose.yml` y la configuración para los contenedores que serán desplegados en la máquina virtual.
  - **`docker-compose.yml`**: Define los servicios que se desplegarán:
    - **Nginx**: Para gestionar el tráfico con un proxy inverso.
    - **Let’s Encrypt**: Para obtener automáticamente un certificado SSL.
    - **MonoMap API**: La imagen de la API MonoMap obtenida desde Docker Hub.
    - **MongoDB**: Base de datos para almacenar los datos de la API MonoMap.

- **`modules/vm/`**: Módulos de Terraform para la creación y configuración de la máquina virtual en la nube.
  - **`main.tf`**: Archivo principal para el despliegue de la infraestructura.
  - **`variables.tf`**: Contiene las variables para el despliegue.
  - **`providers.tf`**: Configura los proveedores de Terraform (Azure en este caso).
  - **`outputs.tf`**: Define los valores que se retornan después del despliegue, como la IP de la VM.

- **`.gitignore`**: Define qué archivos deben ser ignorados en el repositorio Git.

## Despliegue de la Infraestructura

Este proyecto utiliza **Terraform** para gestionar la infraestructura y **GitHub Actions** para automatizar el proceso de despliegue y destrucción. A continuación se detalla el flujo de despliegue:

### Requerimientos Previos

1. **Cuenta de Azure**: Debes tener una cuenta de Azure configurada y permisos para crear máquinas virtuales y contenedores de almacenamiento.

2. **GitHub Actions**: Asegúrate de tener acceso a GitHub Actions y configurar correctamente los secretos necesarios en tu repositorio:
   - `ARM_CLIENT_ID`
   - `ARM_CLIENT_SECRET`
   - `ARM_SUBSCRIPTION_ID`
   - `ARM_TENANT_ID`
   - `SSH_PRIVATE_KEY`
   - `SSH_PUBLIC_KEY`
   - `TF_VAR_DOMAIN`
   - `TF_VAR_MAIL_SECRET_KEY`
   - `TF_VAR_MAIL_SERVICE`
   - `TF_VAR_MAIL_USER`
   - `TF_VAR_MAPBOX_ACCESS_TOKEN`
   - `TF_VAR_MONGO_DB`
   - `TF_VAR_MONGO_INITDB_ROOT_PASSWORD`
   - `TF_VAR_MONGO_INITDB_ROOT_USERNAME`
   - `TF_VAR_MONGO_URL`
   - `TF_VAR_PORT`

### Pasos para el Despliegue

1. **Clonar el repositorio**:

   ```bash
   git clone https://github.com/tu-usuario/711-incident-infrastructure.git
   cd 711-incident-infrastructure

Configurar el Backend de Terraform: El estado de Terraform se almacena en un contenedor de Azure Storage. Para configurarlo, asegúrate de definir las variables de tu contenedor en `providers.tf`.

Desplegar la infraestructura:

Al hacer push en la rama `main`, el archivo `.github/workflows/deploy-dev.yml` se ejecutará automáticamente, lo que desplegará una máquina virtual con Docker instalado y configurará los contenedores definidos en `docker-compose.yml`.

```bash
git add .
git commit -m "Desplegando infraestructura"
git push origin main

Verificar el Despliegue: Una vez finalizado, puedes obtener la IP pública de la máquina virtual a través de los outputs de Terraform y acceder a la API de MonoMap a través del proxy Nginx, asegurado con SSL por Let's Encrypt.

Destrucción de la Infraestructura
Para destruir manualmente la infraestructura, puedes usar el flujo destroy-dev.yml mediante el trigger manual workflow_dispatch. Esto eliminará la máquina virtual y los recursos asociados en Azure.

Estructura de la Máquina Virtual
La máquina virtual desplegada incluye Docker preinstalado y ejecuta los siguientes servicios a través de Docker Compose:

- Nginx (Reverse Proxy): Gestiona el tráfico y redirige las solicitudes HTTPS.
- Let’s Encrypt: Configura y renueva automáticamente los certificados SSL.
- MonoMap API: La API principal, desplegada a través de Docker Hub.
- MongoDB: Base de datos para la API.
- Almacenamiento del Estado
- El estado de Terraform se guarda en un contenedor de Azure Storage para garantizar la persistencia y seguridad. Esto asegura que el     estado de la infraestructura sea consistente entre múltiples despliegues.
