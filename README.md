``md id="4" # Rework Generator — Proceso Creación de Chocolate

Proyecto Final · Paradigmas de Programación  
UABC Campus Otay · 4to Semestre  
Fecha de entrega: 26-MAY-2026

## Integrantes

- Zoe Crespo
- Ingrid Montesinos
- Karla Aparicio

---

# Introducción

Un generador automático de strings de retrabajo para procesos de producción.

En manufactura, cuando algo sale mal en un paso del proceso, el producto se desvía a un flujo de retrabajo y después regresa al flujo principal. Configurar eso manualmente es tedioso y propenso a errores.

Este programa toma como input un flujo principal con sus pasos, los flujos de retrabajo y las razones de cada uno, y genera automáticamente el texto de configuración en este formato:

```text
GoToFlowPath[Flujo Retrabajo/Primer Paso] ReturnStep[Paso de Retorno] Reason[Razon];
```

---

# Diario de investigación — Proceso de solución

## Día 1 — Entendiendo el problema

Cuando nos asignaron la tarea honestamente no me quedó tan claro qué había que hacer.

El enunciado hablaba de “flujos de retrabajo” y “strings de configuración”, pero no era obvio cómo se conectaba todo.

Lo primero que hice fue analizar la plantilla Excel (`Rework_Generator.xlsx`) que nos dio el profesor.

Tiene dos hojas:

### FlowStructures
Los flujos principales con sus pasos. Algunos pasos ya traen la columna `REWORKS` con el output esperado.

### FlowReworks
Los flujos de retrabajo agrupados por razón (`Reason`).

También revisé el diagrama de Excalidraw que compartió el profesor en Classroom.

Ahí entendí que el output es un string compuesto de tres partes:

- `GoToFlowPath`
- `ReturnStep`
- `Reason`

El diagrama mostraba un ejemplo con latas de leche que me ayudó a comprender mejor el concepto.

### Conclusión del día

El problema en realidad es tomar datos de una tabla y ensamblar un string con un formato fijo.

No es tan complicado como parecía al principio.

---

## Día 2 — Eligiendo la herramienta

Una vez que entendimos el patrón, discutimos en equipo qué herramienta usar.

### Opción A — Python puro + terminal

Lo más simple, pero el output sería solo texto en consola.

No se ve bien y es difícil de usar para alguien que no sabe programar.

### Opción B — Excel con macros

El profesor ya dio la plantilla en Excel, podríamos hacer fórmulas.

Pero las macros de Excel son complicadas y no queríamos que el profesor tuviera que habilitarlas.

### Opción C — App web en HTML

Un solo archivo `.html` que funciona abriéndolo en el navegador.

Sin instalaciones, sin dependencias, funciona en cualquier computadora.

### Decisión

Elegimos la Opción C.

La razón principal: cualquier persona puede abrir un archivo HTML con doble clic.

No necesita instalar Python, Node ni nada adicional.

### Herramienta utilizada

Usamos Claude (IA de Anthropic) para ayudarnos a generar el código base de la aplicación.

Le describimos el problema, le mostramos la plantilla `.xlsx` y el formato de output esperado, y nos generó la estructura inicial.

Luego nosotros revisamos, ajustamos y validamos que el output fuera correcto.

---

## Día 3 — Implementación y errores

Con el código base generado por Claude, empezamos a probar con el ejemplo del enunciado (proceso de leche).

### Error 1

Al principio el programa separaba los retrabajos con salto de línea (`\n`) pero el formato correcto del `.xlsx` es concatenarlos en una sola celda separados por espacio.

Tuvimos que cambiar:

```javascript
join('\n')
```

por:

```javascript
join(' ')
```

### Error 2

Nos confundimos con `ReturnStep`.

Pensamos que siempre era el siguiente paso en el proceso, pero en realidad es el paso al que regresa después del retrabajo — que puede ser el mismo paso u otro anterior.

Lo entendimos mejor viendo el ejemplo de “Empacar Lata” en el enunciado.

### Error 3

La interfaz inicialmente no mostraba bien los strings cuando un paso tenía múltiples retrabajos.

Tuvimos que ajustar cómo se renderizaba el output para que fuera legible.

### Validación

Comparamos nuestro output contra el ejemplo del enunciado:

```text
Empacar Lata →
GoToFlowPath[Retrabajar Leche/Hervir Leche] ReturnStep[Empacar Lata] Reason[Leche Podrida];

GoToFlowPath[Retrabajar Empaque/Desempacar] ReturnStep[Enviar Lata] Reason[Empaque Dañado];
```

Cuando nuestro programa generó exactamente ese output, supimos que la lógica estaba correcta.

---

## Día 4 — Aplicando al proceso de chocolate

Con la lógica validada, modelamos el proceso completo de creación de chocolate.

| # | Paso | Retrabajos |
|---|---|---|
| 1 | Recepción de Cacao | — |
| 2 | Limpieza y Selección | Cacao Contaminado |
| 3 | Tostado de Cacao | Cacao Quemado · Cacao Crudo |
| 4 | Descascarillado | Cáscara Incompleta |
| 5 | Molienda de Cacao | — |
| 6 | Mezclado y Conchado | Textura Incorrecta · Sabor Fuera de Especificación |
| 7 | Temperado | Temperatura Incorrecta |
| 8 | Moldeado | Forma Defectuosa |
| 9 | Enfriado y Solidificación | — |
| 10 | Desmoldado | Chocolate Pegado |
| 11 | Inspección de Calidad | Falla Visual · Fuera de Estándar |
| 12 | Empaque | Empaque Dañado |
| 13 | Despacho | — |

---

## Ejemplo de output generado para Tostado de Cacao (Pos. 3)

```text
GoToFlowPath[Retrabajo Tostado/Re-Tostar] ReturnStep[Tostado de Cacao] Reason[Cacao Quemado];

GoToFlowPath[Retrabajo Tostado/Tostado Extra] ReturnStep[Tostado de Cacao] Reason[Cacao Crudo];
```

---

## Ejemplo de output generado para Inspección de Calidad (Pos. 11)

```text
GoToFlowPath[Retrabajo Calidad/Re-Inspeccionar] ReturnStep[Inspección de Calidad] Reason[Falla Visual];

GoToFlowPath[Retrabajo Calidad/Reproceso Total] ReturnStep[Mezclado y Conchado] Reason[Fuera de Estandar];
```

---

# Cómo usarlo

1. Descarga `rework_generator.html`
2. Doble clic para abrirlo en tu navegador
3. El proceso de chocolate se carga automáticamente
4. Puedes agregar o editar pasos y retrabajos desde el panel izquierdo
5. Presiona **“Generar Output”**
6. Copia el string generado con el botón **“Copiar”**

No requiere instalación, servidor ni conexión a internet.

---

# Estructura del repositorio

```text
rework-generator/
├── rework_generator.html ← App completa (abrir en navegador)
└── README.md ← Este documento
```

---

# Lógica central del generador

```javascript
// Para cada paso, construir su string de retrabajo
function buildString(stepId, stepName) {
  return reworks[stepId]
    .filter(r => r.flowPath || r.reason)
    .map(r =>
      `GoToFlowPath[${r.flowPath}] ReturnStep[${r.returnStep || stepName}] Reason[${r.reason}];`
    )
    .join(' ');
}
```

---

# Conclusión

Este proyecto nos dejó varias lecciones que van más allá del código en sí.

Al principio subestimamos el problema.

Pensamos que era solo “concatenar strings” y que estaría listo en una hora.

Pero la parte difícil no fue programar — fue entender exactamente qué quería el cliente (en este caso el profesor actuando como cliente).

El formato del output, cuándo se concatenan los retrabajos, qué significa `ReturnStep` en contexto real de manufactura… todo eso requirió análisis, comparar el diagrama con la plantilla, y hacer pruebas hasta que el output coincidiera exactamente con el esperado.

Otra cosa importante fue la decisión de usar una app web en HTML en lugar de Python o Excel.

Técnicamente cualquiera de las tres opciones resolvía el problema, pero la solución correcta no siempre es la más sofisticada — es la que mejor se adapta al usuario final.

Un archivo HTML que se abre con doble clic es más útil para un operador de manufactura que un script de Python que requiere terminal.

El uso de IA (Claude) como herramienta de desarrollo también fue una experiencia interesante.

No fue un atajo: todavía tuvimos que entender el problema, validar el output, corregir errores y tomar decisiones de diseño.

La IA aceleró la escritura del código, pero el criterio de qué era correcto o incorrecto siempre fue nuestro.

Eso es exactamente como funciona en la industria real.

## En resumen

El core técnico es simple, pero llegar a él requirió un proceso de análisis, iteración y toma de decisiones que es el verdadero aprendizaje del proyecto.
```md id="5"

