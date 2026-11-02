# Prompts

Aquí van **todos los prompts que lanzaste** para hacer el ejercicio, en el orden en que los
lanzaste, con el modelo y la herramienta de cada uno.

Esto no es papeleo. Lo que se revisa es **cómo pediste las cosas**, no solo lo que salió: un
resultado flojo con un prompt bueno y un resultado flojo con un prompt vago necesitan feedback
distinto, y sin este archivo no se distinguen.

## Cómo rellenarlo

- Un apartado `## Prompt N` por cada prompt.
- **Pega el prompt tal cual lo lanzaste**, dentro del bloque de código, aunque ocupe diez líneas
  y aunque tenga faltas. No lo reescribas para que quede bien: el que arreglaste mentalmente
  después no es el que lanzaste.
- Incluye también los que **no funcionaron**. Suelen ser los más útiles de leer.
- `Modelo` y `Herramienta` en todos. Si cambiaste de una a otra a mitad, se nota aquí.

Borra el ejemplo de abajo cuando escribas el primero.

---

## Prompt 1

**Modelo:** Opus 5.5 1M xHigh
**Herramienta:** Claude Code

```
Necesito escribir una especificación viva del vertical de cuentas y acceso que ya existe en este proyecto.

Explora el código del backend y del frontend antes de escribir. Cubre únicamente:
- registro de usuario;
- inicio de sesión;
- sesión y cierre de sesión;
- consulta del perfil;
- protección de rutas;
- pantallas de acceso relacionadas.

No implementes cambios de código ni corrijas posibles problemas. Solo crea el archivo docs/spec-viva/rm.md.

El archivo debe estar en castellano y cumplir exactamente este formato:
- una sección ## Purpose de una o dos frases;
- una sección ## Requirements;
- varios apartados ### Requirement:;
- cada requisito debe describir comportamiento observable y usar SHALL;
- cada requisito debe tener al menos un #### Scenario:;
- cada escenario debe contener exactamente o como mínimo estas viñetas:
  - **WHEN**
  - **THEN**

No uses secciones ADDED, MODIFIED ni REMOVED.
No menciones nombres de clases, nombres de archivos ni detalles internos de implementación.
No documentes tareas, backlog ni funcionalidades fuera de cuentas y acceso.

Después de crear el archivo, indícame cuántos requisitos escribiste y qué partes del código consultaste.
```

**Qué salió:** Funcionó a la primera. El agente escribió 12 requisitos y 43 escenarios, pero después realizó acciones de entrega que todavía estaban pendientes.
