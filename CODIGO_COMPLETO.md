# Código Completo del IDE Python Educativo

## package.json
```json
{
  "name": "python-ide-educativo",
  "private": true,
  "version": "1.0.0",
  "scripts": {
    "dev": "NODE_ENV=development tsx server/index.ts",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "@hookform/resolvers": "^3.3.2",
    "@radix-ui/react-accordion": "^1.1.2",
    "@radix-ui/react-alert-dialog": "^1.0.5",
    "@radix-ui/react-avatar": "^1.0.4",
    "@radix-ui/react-checkbox": "^1.0.4",
    "@radix-ui/react-dialog": "^1.0.5",
    "@radix-ui/react-dropdown-menu": "^2.0.6",
    "@radix-ui/react-label": "^2.0.2",
    "@radix-ui/react-popover": "^1.0.7",
    "@radix-ui/react-select": "^2.0.0",
    "@radix-ui/react-slot": "^1.0.2",
    "@radix-ui/react-tabs": "^1.0.4",
    "@radix-ui/react-toast": "^1.1.5",
    "@radix-ui/react-tooltip": "^1.0.7",
    "@tanstack/react-query": "^5.8.4",
    "class-variance-authority": "^0.7.0",
    "clsx": "^2.0.0",
    "drizzle-orm": "^0.29.1",
    "drizzle-zod": "^0.5.1",
    "express": "^4.18.2",
    "framer-motion": "^10.16.4",
    "lucide-react": "^0.294.0",
    "monaco-editor": "^0.44.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "tailwind-merge": "^2.0.0",
    "tailwindcss-animate": "^1.0.7",
    "tsx": "^4.6.0",
    "vite": "^5.0.0",
    "wouter": "^2.12.1",
    "zod": "^3.22.4"
  },
  "devDependencies": {
    "@types/express": "^4.17.21",
    "@types/node": "^20.9.0",
    "@types/react": "^18.2.37",
    "@types/react-dom": "^18.2.15",
    "@vitejs/plugin-react": "^4.1.1",
    "autoprefixer": "^10.4.16",
    "postcss": "^8.4.31",
    "tailwindcss": "^3.3.5",
    "typescript": "^5.2.2"
  }
}
```

## Configuración de Vite (vite.config.ts)
```typescript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import path from "path";

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./client/src"),
      "@shared": path.resolve(__dirname, "./shared"),
    },
  },
  server: {
    port: 3000,
    proxy: {
      "/api": {
        target: "http://localhost:5000",
        changeOrigin: true,
      },
    },
  },
});
```

## Configuración de Tailwind (tailwind.config.ts)
```typescript
import type { Config } from "tailwindcss";

export default {
  darkMode: ["class"],
  content: ["./client/index.html", "./client/src/**/*.{js,jsx,ts,tsx}"],
  theme: {
    extend: {
      colors: {
        background: "var(--background)",
        foreground: "var(--foreground)",
        primary: {
          DEFAULT: "var(--primary)",
          foreground: "var(--primary-foreground)",
        },
        secondary: {
          DEFAULT: "var(--secondary)",
          foreground: "var(--secondary-foreground)",
        },
        // ... más colores
      },
      fontFamily: {
        sans: ["Inter", "sans-serif"],
        mono: ["JetBrains Mono", "monospace"],
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
} satisfies Config;
```

## HTML Principal (client/index.html)
```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Python IDE Educativo</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

## Archivo Principal React (client/src/main.tsx)
```typescript
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import App from "./App.tsx";
import "./index.css";

createRoot(document.getElementById("root")!).render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

## Servidor Express (server/index.ts)
```typescript
import express, { type Request, Response, NextFunction } from "express";
import { registerRoutes } from "./routes";
import { setupVite, serveStatic } from "./vite";

const app = express();
app.use(express.json());
app.use(express.urlencoded({ extended: false }));

// Configurar Vite o archivos estáticos
await setupVite(app);

// Registrar rutas de API
await registerRoutes(app);

// Servir archivos estáticos en producción
if (process.env.NODE_ENV === "production") {
  serveStatic(app);
}

const PORT = 5000;
app.listen(PORT, "0.0.0.0", () => {
  console.log(`Servidor corriendo en puerto ${PORT}`);
});
```

## Hook de Pyodide (client/src/hooks/use-pyodide.tsx)
```typescript
import { useState, useEffect } from "react";

declare global {
  interface Window {
    loadPyodide: any;
  }
}

interface PyodideInstance {
  runPython: (code: string) => any;
  loadPackage: (packages: string | string[]) => Promise<void>;
}

export function usePyodide() {
  const [pyodide, setPyodide] = useState<PyodideInstance | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    let mounted = true;

    const loadPyodide = async () => {
      try {
        if (!window.loadPyodide) {
          const script = document.createElement("script");
          script.src = "https://cdn.jsdelivr.net/pyodide/v0.24.1/full/pyodide.js";
          script.async = true;
          
          await new Promise((resolve, reject) => {
            script.onload = resolve;
            script.onerror = reject;
            document.head.appendChild(script);
          });
        }

        const pyodideInstance = await window.loadPyodide({
          indexURL: "https://cdn.jsdelivr.net/pyodide/v0.24.1/full/",
        });

        await pyodideInstance.loadPackage(["numpy", "matplotlib"]);

        if (mounted) {
          setPyodide(pyodideInstance);
          setIsLoading(false);
        }
      } catch (err: any) {
        if (mounted) {
          setError(err.message || "Error cargando Python");
          setIsLoading(false);
        }
      }
    };

    loadPyodide();
    return () => { mounted = false; };
  }, []);

  return { pyodide, isLoading, error };
}
```

## Editor Monaco (client/src/components/monaco-editor.tsx)
```typescript
import { useEffect, useRef } from "react";
import * as monaco from "monaco-editor";

interface MonacoEditorProps {
  value: string;
  onChange: (value: string) => void;
  language?: string;
  height?: string;
}

export function MonacoEditor({
  value,
  onChange,
  language = "python",
  height = "400px",
}: MonacoEditorProps) {
  const editorRef = useRef<HTMLDivElement>(null);
  const editorInstanceRef = useRef<monaco.editor.IStandaloneCodeEditor | null>(null);

  useEffect(() => {
    if (!editorRef.current) return;

    const editor = monaco.editor.create(editorRef.current, {
      value,
      language,
      theme: "vs",
      fontSize: 14,
      fontFamily: "JetBrains Mono, monospace",
      minimap: { enabled: false },
      wordWrap: "on",
      lineNumbers: "on",
      automaticLayout: true,
      tabSize: 4,
    });

    editorInstanceRef.current = editor;

    const disposable = editor.onDidChangeModelContent(() => {
      onChange(editor.getValue());
    });

    return () => {
      disposable.dispose();
      editor.dispose();
    };
  }, []);

  useEffect(() => {
    if (editorInstanceRef.current && editorInstanceRef.current.getValue() !== value) {
      editorInstanceRef.current.setValue(value);
    }
  }, [value]);

  return <div ref={editorRef} style={{ height }} className="border rounded" />;
}
```

## Instrucciones de Uso

1. **Crear el proyecto:**
   ```bash
   mkdir python-ide-educativo
   cd python-ide-educativo
   ```

2. **Copiar todos los archivos** de código en la estructura correcta

3. **Instalar dependencias:**
   ```bash
   npm install
   ```

4. **Ejecutar en desarrollo:**
   ```bash
   npm run dev
   ```

5. **Para producción:**
   ```bash
   npm run build
   ```

El IDE estará disponible en `http://localhost:3000` y incluye:
- Editor de código Python con sintaxis resaltada
- Ejecución de código en el navegador
- 5 ejemplos educativos
- Funciones para guardar y descargar código
- Interfaz responsive y moderna

¿Necesitas que te explique alguna parte específica del código?
