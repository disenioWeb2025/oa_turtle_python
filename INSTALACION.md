# Instalación del IDE Python Educativo

## Descripción
Este es un IDE completo de Python que funciona directamente en el navegador, perfecto para que tus estudiantes puedan programar sin instalar nada.

## Características
- ✅ Editor de código con resaltado de sintaxis
- ✅ Ejecución de Python en el navegador (sin servidor)
- ✅ 5 ejemplos educativos pre-cargados
- ✅ Guardar y descargar código
- ✅ Interfaz moderna y responsive
- ✅ No requiere instalación de Python

## Requisitos
- Node.js 18 o superior
- NPM o Yarn

## Instalación Paso a Paso

### 1. Descargar e instalar dependencias
```bash
# Clonar o descargar el proyecto
# Navegar al directorio del proyecto
cd python-ide-educativo

# Instalar dependencias
npm install
```

### 2. Ejecutar en desarrollo
```bash
npm run dev
```

### 3. Construir para producción
```bash
npm run build
```

## Estructura del Proyecto
```
python-ide-educativo/
├── client/                 # Frontend React
│   ├── src/
│   │   ├── components/     # Componentes de UI
│   │   ├── pages/         # Páginas de la aplicación
│   │   ├── hooks/         # Hooks personalizados
│   │   └── lib/           # Utilidades
├── server/                # Backend Express
├── shared/                # Tipos compartidos
└── package.json
```

## Personalización

### Cambiar ejemplos de código
Edita el archivo `server/storage.ts` para modificar los ejemplos que aparecen en la barra lateral.

### Modificar colores y estilos
Edita `client/src/index.css` para cambiar los colores del tema.

### Agregar nuevas funcionalidades
Los componentes están en `client/src/components/` y son fáciles de modificar.

## Despliegue

### Para Replit
El proyecto ya está configurado para Replit. Solo necesitas:
1. Hacer fork del proyecto
2. Ejecutar `npm run dev`

### Para otros proveedores
1. Construir el proyecto: `npm run build`
2. Subir los archivos a tu servidor web
3. Configurar el servidor para servir archivos estáticos

## Soporte
Este IDE usa Pyodide para ejecutar Python en el navegador, por lo que funciona completamente offline una vez cargado.
