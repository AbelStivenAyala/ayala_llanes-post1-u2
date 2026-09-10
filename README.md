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

### Decisión 3 — Builder vs. constructor telescópico vs. setters (Parte 2)
Patrón elegido: Builder

Justificación: Se descartó el constructor con los 9 parámetros porque
el cliente debería recordar el orden exacto de los argumentos, y con
varios parámetros del mismo tipo (String, boolean) es fácil invertirlos
sin que el compilador lo detecte. Se descartaron los constructores
sobrecargados porque, con 8 parámetros opcionales, el número de
combinaciones razonables crece rápidamente y generaría demasiados
constructores casi idénticos. Se descartó también una clase mutable
con setters sueltos porque el objeto podría quedar a medio configurar,
y no habría un punto único donde validar la consistencia de la
combinación de valores (por ejemplo, exigir outputPath cuando
compress=true). Builder resuelve las tres limitaciones: expone una
API fluida y legible, y centraliza la validación de estados
inconsistentes en build(), antes de construir el objeto.

### Decisión 4 — ¿ReportFactoryRegistry necesita ser Singleton? (Parte 2)
Conclusión: NO

Justificación: El campo estático REGISTRY ya garantiza una única
fuente de verdad por JVM sin necesidad de la maquinaria de Singleton
(constructor privado con guardas + getInstance()). Además, no existe
en el sistema ningún punto donde se necesite pasar el registro como
objeto (mock, inyección por constructor), y su inicialización —llenar
un Map con tres entradas— no es costosa, por lo que no se beneficia de
inicialización perezosa. Convertirlo en Singleton agregaría ceremonia
sin resolver un problema real, y de hecho limitaría un escenario
futuro razonable (una plataforma multi-institución con un registro
independiente por institución).

## Herramientas utilizadas
- Java 17, Apache Maven, VS Code, Git, GitHub

## Conclusiones
[Párrafo de 3-5 oraciones con los aprendizajes más relevantes de
ambas partes, especialmente sobre el proceso de decidir entre
patrones y no solo implementarlos]
