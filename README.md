# Spec-Driven Development: cómo usar la IA en el desarrollo de software

Este repositorio acompaña la exposición **"Spec-Driven Development: cómo usar la IA en el desarrollo de software"**, dada en la **Universidad Abierta Interamericana (UAI)**, sede Rosario. La charla forma parte del proyecto de extensión *Proyecto de Capacitación en Inteligencia Artificial Aplicada al Desarrollo de Software*, de la Facultad de Tecnología Informática.

- **Expositor:** Santiago Montironi
- **Auxiliar:** Luca Bianconi Bo

**Presentación:** [Carpeta Google Drive](https://drive.google.com/drive/folders/1zwq8dee5TF2XkoZYD5cSjOCmx9ww9WfS?usp=drive_link)

## ¿Qué es Spec-Driven Development?

Spec-Driven Development (SDD) es desarrollar a partir de una especificación que define qué tiene que hacer el sistema y cómo se tiene que comportar. Esa especificación funciona como contrato y fuente de verdad del proyecto. Guía cada decisión, y el código se escribe a partir de ella, no al revés.

Cuando se trabaja con IA, SDD evita el *vibe coding*, que es pedirle a un agente que programe sin haber definido qué tiene que construir. Sin contexto, la IA elige los datos, la seguridad y las librerías por su cuenta, agrega dependencias que nadie pidió e ignora la estructura del proyecto. Con SDD, el agente recibe ese contexto en lugar de adivinarlo.

## Qué contiene este repositorio

El repositorio tiene los tres archivos del ejemplo práctico de la charla: un panel de administración con login y CRUD de productos.

```
.
├── .claude/
│   ├── context.md
│   ├── spec.md
│   └── rules.md
└── README.md
```

| Archivo | Pregunta que responde | Qué define |
|---|---|---|
| `context.md` | ¿De qué trata el proyecto? | Objetivo, usuarios, alcance y lo que queda fuera de alcance |
| `spec.md` | ¿Cómo se construye? | Stack, endpoints, modelo de datos, validaciones, casos borde y criterios de aceptación |
| `rules.md` | ¿Qué puede y qué no puede generar la IA? | Convenciones, estructura de carpetas, cómo generar cada cosa y lo que está prohibido |

Comparado con una obra, `context.md` es el terreno y para quién es la casa, `spec.md` son los planos y `rules.md` es el código de edificación.

El repositorio no incluye el código de la aplicación. El objetivo es mostrar cómo se especifica un proyecto antes de pedirle a la IA que lo implemente.

### El ejemplo

Un administrador inicia sesión y gestiona productos con nombre, precio y stock.

- **Frontend:** React + TypeScript (Vite)
- **Backend:** Node.js + Express + TypeScript
- **Base de datos:** MongoDB con Mongoose
- **Autenticación:** JWT en una cookie HttpOnly

## Cómo usar los archivos en tu proyecto

1. Copiá la carpeta `.claude/` en la raíz de tu proyecto y adaptá los tres archivos.
2. Referencialos desde el archivo de instrucciones de tu herramienta: `CLAUDE.md` en Claude Code, `AGENTS.md` en otros agentes o `.cursor/rules` en Cursor. Por ejemplo, en `CLAUDE.md`:

```markdown
Antes de cualquier tarea, leé estos archivos:
- @.claude/context.md
- @.claude/spec.md
- @.claude/rules.md
```

3. Seguí el flujo de trabajo:
   1. **Especificar:** escribir `context.md`, `spec.md` y `rules.md`.
   2. **Aclarar:** el agente señala las ambigüedades y se resuelven.
   3. **Planificar:** el agente propone un plan y se revisa.
   4. **Implementar:** el agente escribe el código y se verifica contra los criterios de aceptación de `spec.md`.

## Buenas prácticas

- **Actualizar:** si cambia un requisito, se actualizan los archivos.
- **Revisar:** el código generado se lee y se prueba.
- **Seguridad:** las reglas son explícitas y los permisos, mínimos.
- **Datos sensibles:** nada de claves ni datos reales de clientes.
- **Proporción:** un proyecto chico no necesita todo esto.
- **Criterio:** la responsabilidad sigue siendo tuya.
