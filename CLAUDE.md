# dixon@shell

Landing page de una sola página (`index.html`) que funciona como cheatsheet interactivo de terminal para retener comandos y atajos. Todo — HTML, CSS, JS y una fuente (`JBM`, JetBrains Mono) — vive embebido en ese único archivo; no hay build step, bundler ni dependencias externas.

## Propósito

Ayudar a un desarrollador a memorizar/repasar comandos de uso frecuente mediante:
- Una secuencia de "boot" animada tipo terminal al cargar la página.
- Un carrusel de **tips** rotativos (se auto-avanza cada 9s, o navegación manual con las flechas).
- Cheatsheets por pestañas, con buscador para filtrar comandos en vivo.

## Estructura de `index.html`

- **CSS embebido** (`<style>` en el `<head>`): tema terminal oscuro, variables de color por categoría (`--accent` verde/git, `--amber` linux/claude, `--blue` docker, `--violet` sql).
- **Fuente embebida**: JetBrains Mono como `data:font/woff2;base64,...` — por eso el archivo pesa ~650KB. No tocar ese bloque salvo para cambiar de fuente.
- **Boot sequence** (`renderBoot`): simula tipeo de `whoami` / `echo $STATUS`. Respeta `prefers-reduced-motion`.
- **`TIPS` (array)**: cada tip tiene `{ cat, color, text }`. `cat` debe coincidir con una clave de `DATA` y `color` con una clave de `catColorVar`.
- **`DATA` (objeto)**: una clave por pestaña (`git`, `linux`, `docker`, `sql`, `claude`). Cada una es un array de grupos `{ grp: "Nombre del grupo", items: [[comando, descripción], ...] }`.
- **Render de paneles/tabs y filtro de búsqueda**: al final del script, construye el DOM de `DATA` y conecta el input de búsqueda (`#filterInput`) para filtrar por texto en comando o descripción.

## Cómo agregar contenido

**Nuevo comando a un cheatsheet existente**: añade una entrada `[comando, descripción]` dentro del grupo (`grp`) correspondiente en `DATA`. Si el grupo no existe, créalo como `{ grp: "...", items: [...] }`.

**Nueva categoría/pestaña completa** (ej. "kubernetes"):
1. Agrega un `<button class="tabbtn" data-tab="k8s">...</button>` en `#tabbar`.
2. Agrega la clave `k8s: [...]` en `DATA` con sus grupos.
3. Agrega `k8s: "K8S"` en `catLabel` y, si usas un color nuevo, defínelo en `catColorVar` y como variable CSS.

**Nuevo tip rotativo**: añade un objeto al array `TIPS` con `cat` existente. El contador (`01 / 39`) y los dots se generan solos a partir de `TIPS.length` — no hay que actualizar números a mano.

## Convenciones

- Todo en español (comandos con descripción en español, tono directo y técnico).
- Descripciones de comandos: una frase corta, explica el *qué* y a veces el *cuándo/por qué* usarlo en vez de otro similar (ej. diferencias entre `kill -9` y `kill -15`).
- Tips: más largos que las descripciones de cheatsheet, suelen justificar *por qué* importa el comando o qué problema evita.
- No hay build ni linter: los cambios se validan abriendo `index.html` directamente en el navegador.
- Mantener el archivo como HTML plano vanilla JS (sin frameworks, sin módulos, sin npm) — es una decisión intencional de simplicidad y portabilidad (un solo archivo, cero dependencias).

## Verificar cambios

No hay servidor ni build: abre `index.html` directamente en el navegador (doble clic o `start index.html` en Windows) y revisa la pestaña afectada, el filtro de búsqueda y que el contador de tips sea correcto si tocaste `TIPS`.
