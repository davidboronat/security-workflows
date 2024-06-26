# Guía de Uso del Flujo de Trabajo (Workflow) Reutilizable para Análisis SAST y SCA

Este flujo de trabajo permite realizar análisis de Seguridad Estática de Aplicaciones (SAST) y Análisis de Composición de Software (SCA) en tu repositorio, facilitando la identificación y gestión de vulnerabilidades de seguridad en las dependencias del proyecto y el código fuente.

## Configuración Requerida

Para incorporar el análisis SAST y SCA en tu proyecto, sigue estos pasos para configurar el flujo de trabajo en tu repositorio.

### 1. Crear el Archivo de Flujo de Trabajo

Crea un nuevo archivo en tu repositorio bajo el directorio `.github/workflows/` con el nombre que prefieras, por ejemplo, `security_scans_config.yml`.

### 2. Configurar el Contenido del Flujo de Trabajo

Pega el siguiente contenido en el archivo `security_scans_config.yml` creado. Asegúrate de ajustar los valores de los secretos (`PAT`, `APP_URL` y `APP_TOKEN`) según corresponda a tu configuración.

```yaml
name: Security Scans

on:
  push:
    branches:
      - master  # Ajusta esto según tus necesidades de ramificación

jobs:
  security_scan:
    uses: davidboronat/security-workflows/.github/workflows/security_scans.yml@main
    with:
      enable_sast: true  # Habilita análisis SAST
      enable_sca: true   # Habilita análisis SCA
      repo_url: ${{ github.server_url }}/${{ github.repository }}
      branch_name: ${{ github.ref_name }}
    secrets:
      pat_token: ${{ secrets.PAT }}  # Token de acceso personal para repos privados. Eliminar variable si se trata de repo público
      server: ${{ secrets.APP_URL }}  # URL del servidor de análisis
      app_token: ${{ secrets.APP_TOKEN }}  # Token de autenticación para guardar los resultados en la plataforma cloud. Eliminar si no se trata de un usuario autenticado
```

### 3. Configurar Secretos

Asegúrate de que los secretos `PAT` (Token de Acceso Personal, en caso de analizar un repositorio privado), `APP_URL` (URL del servidor de análisis SAST y SCA) y `APP_TOKEN` (Token de Autenticación para guardar el análisis en el historial de la plataforma) estén configurados en tu repositorio o a nivel de organización:

- Ve a `Settings > Secrets` en tu repositorio de GitHub.
- Haz clic en `New repository secret`.
- Añade `PAT`, `APP_URL` y `APP_TOKEN` como nombres de los secretos y proporciona los valores correspondientes.

### Notas Adicionales

- **`enable_sast`**: Habilita el análisis SAST. Establece en `false` si deseas omitir este análisis.
- **`enable_sca`**: Habilita el análisis SCA. Establece en `false` si deseas omitir este análisis.
- **`repo_url` y `branch_name`**: Estos parámetros se rellenan automáticamente basados en el evento que dispara el flujo de trabajo.
- El secreto **`APP_URL`** debe ser el mismo para todo el mundo (en este caso mi aplicación encargada de realizar los análisis SAST y SCA). Sin embargo, se trata de una aplicación en desarrollo por lo que no se desea hacer público el dominio de la aplicación todavía.

Con estos pasos, tu repositorio estará listo para realizar análisis SAST y SCA en cada `push` a la rama especificada, ayudándote a mantener tu código más seguro.

## Resultados

1. Un resumen de las vulnerabilidades encontradas en los análisis SAST y SCA en formato JSON. Se puede modificar el workflow para detener la ejecución en función de un umbral de vulnerabilidades a elección del usuario.
2. Dos archivos HTML con un desglose detallado de las vulnerabilidades identificadas por Semgrep y Dependency Check, para una revisión visual y detallada.
