# ayala_llanes-post1-u2
"Post-contenido — Exportación de reportes académicos con patrones creacionales justificados".

## Decisiones de diseño

### Decisión 1 — Factory Method vs. Abstract Factory (Parte 1)
Patrón elegido: Abstract Factory

Justificación: El problema requiere crear una familia de dos productos
relacionados (cuerpo y encabezado/pie de página) que deben mantenerse
consistentes dentro del mismo formato — no es válido combinar, por
ejemplo, un cuerpo Excel con un encabezado PDF. Al agregar un formato
nuevo (CSV, planeado a futuro), se necesitará una familia completa de
piezas nuevas (un nuevo body y un nuevo header/footer), no una
implementación aislada. El riesgo central del problema es que se
mezclen piezas de familias distintas y el documento quede
inconsistente, no que se instancie una clase equivocada de forma
aislada. Se descartó Factory Method porque ese patrón resuelve la
variación de un único producto mediante un solo método de creación, y
no garantiza por sí solo que dos productos independientes (cuerpo y
encabezado) pertenezcan siempre al mismo formato dentro de una misma
exportación.

### Decisión 2 — Mecanismo de extensibilidad de formatos (Parte 1)
Opción elegida: Registro dinámico con Map<String, Supplier<ReportFormatFactory>>

Justificación: Un switch o cadena de if/else sobre el string de
formato obligaría a modificar ese mismo método cada vez que se agregue
un formato nuevo (como el CSV planeado), violando el Principio de
Abierto/Cerrado (OCP). Con un registro basado en Map y Supplier, se
puede registrar un formato nuevo (por ejemplo, "csv") simplemente
llamando a ReportFactoryRegistry.register(...), sin tocar ninguna
línea del código ya existente en la clase.
