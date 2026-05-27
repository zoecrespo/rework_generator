# Rework_Generator
Proyecto Final — Paradigmas de Programacion
UABC Campus Otay 
4to Semestre 
MAYO- 26- 2026 
Integrantes del Equipo:
Zoe Crespo 
Ingrid Montesinos 
Karla Aparicio 
--------------------
# Introduccion 
Un generador automático de strings de retrabajo para procesos de producción. Básicamente, en manufactura cuando algo sale mal en un paso del proceso, el producto se desvía a un flujo de retrabajo y después regresa al flujo principal. Configurar eso manualmente es tedioso y propenso a errores.
Este programa toma como input un flujo principal con sus pasos, los flujos de retrabajo y las razones de cada uno, y genera automáticamente el texto de configuración en este formato:
GoToFlowPath[Flujo Retrabajo/Primer Paso] ReturnStep[Paso de Retorno] Reason[Razon];

# Proceso de solución
Entendiendo el problema
Cuando nos asignaron la tarea honestamente no me quedó tan claro qué había que hacer. Lo primero que hice fue analizar la plantilla Excel (Rework_Generator.xlsx) que nos dio el profesor. Tiene dos hojas:

FlowStructures: los flujos principales con sus pasos. Algunos pasos ya traen la columna REWORKS con el output esperado.
FlowReworks: los flujos de retrabajo agrupados por razón (Reason).

También revisé el diagrama de Excalidraw que compartió el profesor en Classroom. Ahí entendí que el output es un string compuesto de tres partes: GoToFlowPath, ReturnStep y Reason. El diagrama mostraba un ejemplo con latas de leche que me ayudó a comprender mejor el concepto.
Diseñando la solución
Una vez que entendí el patrón, lo primero que intenté fue hacerlo directo en Python puro, solo con funciones que recibieran los datos y devolvieran el string. Eso funcionó bien para la lógica central.
El problema fue la interfaz: necesitaba algo que no fuera solo la terminal. Pensé en hacer un script que leyera el xlsx directamente, pero eso limitaba mucho la flexibilidad. Entonces decidí usar Streamlit porque:

Permite hacer una app web sin saber frontend
Se instala con un solo pip install
Tiene componentes para subir archivos, tablas y descargas
El resultado se ve profesional sin diseñar nada desde cero

Dividí el proyecto en dos archivos para separar responsabilidades:

generator.py → solo lógica pura, sin imports de Streamlit
app.py → solo la interfaz, llama a las funciones de generator

# Implementando el generador
La función core es simple. Dado un paso con sus retrabajos, construye el string:
def build_rework_string(reworks: list[dict]) -> str:
    parts = []
    for rw in reworks:
        flow_path   = rw.get("flow_path", "").strip()
        return_step = rw.get("return_step", "").strip()
        reason      = rw.get("reason", "").strip()
        if flow_path or reason:
            parts.append(
                f"GoToFlowPath[{flow_path}] ReturnStep[{return_step}] Reason[{reason}];"
            )
    return " ".join(parts)
    Lo más importante fue entender que múltiples retrabajos en un mismo paso se concatenan en una sola celda, separados por espacio. Eso lo saqué comparando el output esperado en la plantilla xlsx con el diagrama.
Aplicando al proceso de chocolate
Una vez que la lógica funcionó con el ejemplo de leche del enunciado, lo apliqué al proceso real de creación de chocolate.

# Errores que tuve en el camino

Al principio separaba los retrabajos con \n pero el formato correcto es concatenarlos con espacio en la misma celda.
Me confundí con ReturnStep: pensé que siempre era el siguiente paso, pero en realidad es el paso al que regresa en el flujo principal después del retrabajo, que puede ser el mismo u otro anterior.
El modo Excel tardó más de lo esperado porque tuve que decidir cómo estructurar las dos hojas para que fueran compatibles con la plantilla del profesor.

Instalacion y Uso 

# 1. Clonar el repositorio
git clone https://github.com/diegovc20/rework-generator.git
cd rework-generator

# 2. Instalar dependencias
pip install -r requirements.txt

# 3. Ejecutar la app
streamlit run app.py

# Uso
Demo Chocolate: Carga automáticamente el proceso completo de creación de chocolate con todos sus retrabajos. No requiere ningún input.
Modo Excel: Sube un archivo .xlsx con las hojas FlowStructures y FlowReworks en el formato de la plantilla.
Modo Manual: Define tu flujo principal, los flujos de retrabajo con sus razones, y las asignaciones de cada uno a los pasos.
En todos los modos el programa genera el output con el formato requerido y permite descargarlo como CSV.

# ESTRUCTURA 

rework-generator/
├── app.py                  # Interfaz Streamlit (tres modos: Demo, Excel y Manual)
├── generator.py            # Lógica del generador (funciones core)
├── requirements.txt        # Dependencias (streamlit, pandas, openpyxl)
└── README.md               # Este documento

# Conclusión
El proyecto terminó siendo más sencillo de lo que parecía al inicio en cuanto a la estructura y análisis: el core es un generador de strings con un patrón fijo. Lo que tomó más tiempo fue entender exactamente el formato esperado y diseñar la interfaz para que fuera útil más allá del caso puntual del chocolate.
