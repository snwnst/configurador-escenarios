# Configurador de Escenarios Fiscales

Herramienta estática (sin backend) para que negocio categorice cada movimiento del libro mayor
(`credit_line_movement` / catálogo de cuentas contables) dentro de una de las verticales de la
Plataforma de Documentos Fiscales:

- **Estado de Cuenta** — CFDI de Ingresos, método de pago PPD
- **Complemento de pago** — CFDI Complemento de Pago
- **Anticipo** — CFDI de Ingresos, método de pago PUE
- **Retenciones** — CFDI de Retenciones
- **Comisiones** — CFDI de Ingresos, método de pago PUE
- **Cancelaciones** — CFDI de Egresos / cancelación de un CFDI previo. ⚠️ **No desarrollado aún en la
  plataforma**: se incluye solo para que negocio marque su intención sobre estos movimientos; no dispara
  ningún flujo real todavía.
- **Sin categorizar** — el movimiento no dispara ningún CFDI por sí solo

No todos los movimientos tienen que quedar asignados a un escenario — varios son ajustes contables
sin efecto fiscal directo (capital, cancelaciones de comisión, ajustes genéricos).

## Cómo usarlo

Abre `index.html` directamente en el navegador (doble clic, o publicado vía GitHub Pages — ver abajo).
Sigue sin requerir instalación ni build — es un solo archivo — pero **ahora necesita conexión a internet**,
porque carga Tailwind CSS, GSAP (animaciones) y la tipografía Inter desde sus CDNs
(`cdn.tailwindcss.com`, `cdn.jsdelivr.net`, `fonts.googleapis.com`). Sin conexión, la página abre pero se
ve sin estilos ni animaciones (la lógica de categorizar/exportar/importar seguiría funcionando).

> **Nota de honestidad**: se usa Tailwind CSS (vía CDN) + GSAP en vez de shadcn/ui literal — shadcn/ui es
> una librería de componentes React que requiere un paso de build (Vite/Next), lo cual habría significado
> dejar de tener una página estática de un solo archivo. Esta versión imita la estética de shadcn (mismos
> tonos de color, cards con borde suave, botones con las mismas variantes) sin ese costo. Si en algún
> momento sí quieren el shadcn/ui real, es un proyecto aparte (con su propio `npm install`/build/deploy).

1. Pasa el mouse sobre la vertical a la que crees que pertenece el movimiento — la tarjeta se inclina
   hacia esa zona como vista previa de "aquí caería". Haz **click** en la zona para confirmarlo.
2. Cada tarjeta trae una **💡 sugerencia** de referencia — es solo una nota, basada en el análisis
   previo de la plataforma, para ayudarte a decidir. No preasigna nada por sí sola; el punto de esta
   herramienta es que negocio confirme o corrija cada caso.
3. ¿Te equivocaste? El botón **↩️ Deshacer** revierte la última categorización (o reasignación).
4. Tu progreso se guarda automáticamente en este navegador (`localStorage`). Si cierras la pestaña y
   vuelves después, todo sigue ahí.
5. El botón 🌙/☀️ cambia entre tema oscuro (el predeterminado) y claro; tu elección también se
   recuerda en este navegador.

## Cómo compartir tu configuración con otra persona

Esta página es 100% estática — no hay una base de datos compartida ni sincronización automática entre
personas. Para que alguien más vea (o continúe) tu trabajo:

1. Escribe tu nombre en el campo de arriba (queda guardado en el archivo exportado).
2. Click en **⬇️ Exportar configuración** — descarga un `.csv` con tu categorización completa. Se puede
   abrir directamente en Excel/Sheets para revisarlo o editarlo a mano si hace falta.
3. Comparte ese archivo (Slack, correo, o commitéalo a este mismo repo, por ejemplo en
   `configurador-escenarios/exports/`) para que quede versionado y visible a todo el equipo.
4. La otra persona abre la página y usa **⬆️ Importar configuración**, selecciona ese `.csv`, y su
   tablero se actualiza con esa categorización (puede seguir ajustando desde ahí).

El `.csv` exportado tiene este formato (dos filas de metadatos con `#`, luego el encabezado y una fila
por movimiento):

```
# Autor,Juan Perez
# ExportadoEn,2026-09-17T23:23:47.977Z
Codigo,Movimiento,Escenario
4011,DISPOSICION,Sin categorizar
4016,CARGO COMISION POR DISPOSICION,Comisiones
...
```

La columna `Escenario` usa el **nombre** del escenario (no el id interno) — puedes editarla directo en
Excel siempre que el texto coincida exactamente con uno de los 6 nombres de escenario (Estado de Cuenta,
Complemento de pago, Anticipo, Retenciones, Comisiones, Sin categorizar).

### Bonus: enlace directo a una configuración publicada

Si el `.csv` exportado queda accesible por URL pública (por ejemplo, subido a este repo y accedido vía
`raw.githubusercontent.com`), cualquiera puede abrir la página con esa configuración precargada:

```
https://<tu-usuario>.github.io/gbm-scripts/configurador-escenarios/?config=https://raw.githubusercontent.com/<org>/<repo>/main/configurador-escenarios/exports/escenarios-fiscales_2026-09-17.csv
```

Esto solo funciona si el archivo es públicamente accesible por esa URL (el fetch se hace desde el
navegador de quien abre la página, sin ningún servidor intermedio).

## Publicar con GitHub Pages

1. En GitHub: **Settings → Pages → Build and deployment → Source: Deploy from a branch**.
2. Selecciona la rama (`main`) y la carpeta `/configurador-escenarios` (o mueve `index.html` a la raíz
   del repo si tu plan de GitHub Pages no permite elegir subcarpeta).
3. GitHub publica la URL en unos minutos — típicamente
   `https://<tu-usuario>.github.io/gbm-scripts/configurador-escenarios/`.

Si el repo es privado y no tienes GitHub Pages disponible en ese plan, la alternativa es abrir
`index.html` localmente (doble clic) y usar exportar/importar para compartir — la herramienta funciona
igual, solo cambia cómo la gente accede a ella.

## Editar el catálogo de movimientos

El catálogo de 26 movimientos y los 5 escenarios están definidos como arreglos al inicio del `<script>`
en `index.html` (`MOVEMENTS` y `SCENARIOS`). Agregar un movimiento nuevo, cambiar un nombre, o añadir un
escenario es editar esos arreglos — no hay ningún otro archivo de configuración ni build que regenerar.
