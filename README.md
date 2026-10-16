# SnackCheck

**Asistente nutricional inteligente basado en códigos de barras**

SnackCheck es una automatización desarrollada en n8n que consulta información nutricional de productos alimentarios y genera un veredicto claro y comprensible a partir de su código de barras.

## 1. Propósito

### Problema que resuelve

Interpretar las etiquetas nutricionales de los productos puede resultar complicado para muchos consumidores. SnackCheck simplifica este proceso mediante una consulta automática a Open Food Facts y la aplicación de unas reglas de clasificación nutricional previamente definidas.

El sistema ofrece una orientación sencilla sobre la composición del producto, utilizando datos reales disponibles en la base de datos consultada, sin inventar valores nutricionales ausentes.

### Objetivos

- Consultar información nutricional a partir de un código de barras.
- Evaluar los niveles de azúcar, sal y grasa por cada 100 g.
- Incorporar la clasificación Nutri-Score cuando esté disponible.
- Calcular una puntuación de preocupación basada en el número de nutrientes altos.
- Generar un veredicto comprensible y una explicación adaptada al resultado.
- Gestionar las entradas inválidas y los productos sin información suficiente mediante mensajes claros.

### Usuarios objetivo

SnackCheck está dirigido a consumidores que desean comprender mejor los productos que compran, familias que buscan comparar alimentos y personas que necesitan una orientación nutricional rápida y fácil de interpretar.

## 2. Cómo funciona

- El flujo de SnackCheck se ejecuta en n8n y permite consultar la información nutricional de un producto a partir de su código de barras. 
- El sistema obtiene los datos de Open Food Facts, calcula los indicadores nutricionales y genera un veredicto comprensible para el usuario.

### 2.1. Descripción de los nodos

Cada nodo realiza una función específica dentro del proceso de automatización.

|         Nodo                     |                                     Función                                                   |
|----------------------------------|-----------------------------------------------------------------------------------------------|
| Webhook-solicitud barcode        | Recibe la petición HTTP y el código de barras enviado por el usuario.                         |
| Asignar-variable barcode         | Prepara la variable barcode para el procesamientos posteriores.                               |
| Validar barcode                  | Valida el formato del codigo de barras, para avisar al usuario en caso de que no sea valido.  |
| Respond to Webhook Inter (varios)| Devuelven json con datos suficientes para valorar el error por el que barcode no es valido.   |
| HTTP - Llamada API               | Consulta la API de Open Food Facts para obtener la información del producto.                  |
| Respond to Webhook Inter         | Devuelve error 404 por producto no encontrado, con valores json suficientes para valorar.     |
| Validar campos nutricionales     | Valida que el producto esté en la base de datos Open Food Facts.                              |
| Respond to Webhook Inter         | Devuelve error 400 por no haber suficientes información del producto.                         |
| Set Asignar variables            | Selecciona y organiza los datos nutricionales que se utilizarán en los cálculos.              |
| Code-calcula                     | Calcula los indicadores nutricionales, el conteo de semáforo y la puntuación de preocupación. |
| Set Calcular Regla Semaforo      | Especifica la regla semaforo segun valores nutricionales                                      |
| Nodo de IA                       | Genera una explicación del resultado en un lenguaje sencillo y cercano.                       |
| Dar Formato Veredicto            | Construye el texto final con el nombre del producto y la explicación nutricional.             |
| Respond to Webhook Final         | Devuelve la respuesta al cliente que ha realizado la petición, por ejemplo, Hoppscotch.       |


### 2.2. Cálculos nutricionales

El sistema realiza los siguientes cálculos:

- **Conteo de semáforo:** cuenta cuántos nutrientes presentan un nivel alto según los umbrales definidos para azúcar, sal y grasa.
- **Nivel Nutri-Score:** convierte la letra de Nutri-Score en una clasificación: A/B corresponde a saludable, C a moderado y D/E a no saludable.
- **Puntuación de preocupación:** indica el número de nutrientes altos, con un valor comprendido entre 0 y 3.
- **Veredicto general:** combina el conteo de semáforo y Nutri-Score, seleccionando la clasificación más desfavorable cuando ambas están disponibles.

Cuando Nutri-Score no está disponible, se utiliza únicamente el conteo de semáforo, siempre que existan datos nutricionales suficientes. Si faltan los datos necesarios para clasificar el producto, el sistema informa de que no es posible emitir un veredicto.

### 2.3. Generación de la respuesta

Una vez realizados los cálculos, el sistema prepara una respuesta legible que incluye el nombre del producto, el resultado de la evaluación nutricional y una explicación adaptada al nivel de veredicto.

El nodo `Respond to Webhook` devuelve la respuesta al cliente que inició la petición HTTP.


## 3. Configuración

### Requisitos previos

Para utilizar SnackCheck se necesita:

- Una instancia de n8n.
- El workflow de SnackCheck importado en n8n.
- Acceso a la API de Open Food Facts.
- Una herramienta para realizar peticiones HTTP, como Hoppscotch.

### Configuración inicial

1. Importar el workflow en n8n.
2. Comprobar que los nodos están conectados correctamente.
3. Verificar la URL y los parámetros del nodo HTTP Request que consulta Open Food Facts.
4. Configurar el Webhook para recibir las peticiones HTTP.
5. Comprobar que el nodo Respond to Webhook devuelve la respuesta esperada.
6. Realizar pruebas con códigos de barras válidos e inválidos.

### Configuración del modelo de IA

SnackCheck utiliza **Groq** como proveedor de modelos de lenguaje para generar una explicación del veredicto nutricional en un lenguaje sencillo, cercano y comprensible para el usuario.

Para configurar esta funcionalidad es necesario:

1. Disponer de una cuenta en Groq.
2. Obtener una clave API desde la plataforma oficial de Groq: https://console.groq.com/
3. Configurar las credenciales correspondientes en n8n para conectar con el servicio de Groq.
4. Seleccionar el modelo de lenguaje compatible que se utilizará para generar la respuesta.
5. Conectar el nodo de IA con el resto del workflow y comprobar que recibe los datos necesarios para elaborar la explicación.

La clave API debe almacenarse de forma segura en las credenciales de n8n y no debe incluirse directamente en el código del workflow ni en el archivo README.

El nodo de IA utiliza los datos y la clasificación calculados previamente para redactar el veredicto. No debe inventar información nutricional ni sustituir las reglas de clasificación establecidas por el proyecto.

## 4. Uso

### ¿Quién puede utilizar SnackCheck?

SnackCheck está pensado para cualquier persona que quiera conocer de forma rápida y sencilla la calidad nutricional de los productos alimentarios que compra o consume.

Puede resultar especialmente útil para:
- **Consumidores:** para consultar información nutricional antes de comprar un producto.
- **Familias:** para comparar alimentos y tomar decisiones de compra más informadas.
- **Personas interesadas en la alimentación saludable:** para identificar productos con un contenido elevado de azúcar, sal o grasas.

### ¿Cómo se utiliza?

1. El usuario introduce o escanea el código de barras de un producto alimentario.
2. SnackCheck consulta la información del producto en Open Food Facts.
3. La automatización analiza los datos nutricionales disponibles y aplica las reglas de clasificación establecidas.
4. El modelo de IA, mediante Groq, ayuda a presentar el resultado con un lenguaje claro, cercano y fácil de entender.
5. El usuario recibe un veredicto nutricional, acompañado de los principales datos y una breve explicación.

Si el código de barras no es válido, el producto no aparece en la base de datos o faltan datos nutricionales suficientes, SnackCheck informa al usuario de la situación de forma comprensible, sin inventar información.


## 5. Manejo de errores

SnackCheck incorpora mecanismos de validación y control de errores para evitar que los problemas durante la consulta o el análisis de un producto interrumpan la ejecución del flujo. El objetivo es proporcionar al usuario una respuesta clara y útil, incluso cuando no es posible emitir un veredicto nutricional.

### 5.1. Errores contemplados

| Situación                                                         | Tratamiento del error                                                           | Respuesta esperada                                                        |
|-------------------------------------------------------------------|---------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| Formato código de barras no es válido (longitud,vacio,no numerico)| Se valida el formato antes de consultar la API.                                 | json con datos suficientes para solicitar un código de barras válido.     |
| No se recibe ningún código de barras                              | Se comprueba que el dato de entrada exista.                                     | Se informa al usuario de que debe introducir un código de barras.         |
| El producto no existe en Open Food Facts                          | Se comprueba si la consulta devuelve información del producto.                  | Se indica que no se ha encontrado el producto.                            |
| La API no responde o devuelve un error                            | Se controla el resultado de la petición HTTP.                                   | Se informa de que no ha sido posible consultar el producto.               |
| Faltan datos nutricionales                                        | Se comprueba si hay información suficiente para aplicar reglas de clasificación.| Se indica que no hay datos suficientes para emitir un veredicto fiable    |
| No hay información de Nutri-Score                                 | Si hay datos suficientes se utiliza únicamente la clasificación nutricional     | Se genera el veredicto sin utilizar el Nutri-Score.                       |


### 5.2. Principios de gestión de errores

- **No interrumpir innecesariamente el flujo:** los errores previstos deben gestionarse mediante validaciones y ramas específicas.
- **No inventar información:** si no existen datos suficientes, no se debe emitir una clasificación nutricional sin fundamento.
- **Informar con claridad:** los mensajes deben explicar qué ha ocurrido y, cuando sea posible, cómo solucionarlo.
- **Separar los errores técnicos de los nutricionales:** que la API no responda es distinto de que un producto carezca de información nutricional.
- **Mantener la coherencia del veredicto:** el resultado debe respetar las reglas de clasificación definidas, independientemente de la explicación generada por la IA.


## 6. Limitaciones

SnackCheck es una herramienta de orientación nutricional que facilita la interpretación de la información disponible de los productos alimentarios. 
Sus resultados dependen de la calidad de los datos consultados y de las reglas de clasificación implementadas.

### 6.1. Dependencia de Open Food Facts

La información nutricional se obtiene de Open Food Facts. Por tanto:
- No todos los productos están incluidos en la base de datos.
- Algunos productos pueden tener información incompleta, desactualizada o incorrecta.
- La disponibilidad de los datos puede variar según el producto y su registro.
- Si la API no está disponible, no será posible consultar nuevos productos en ese momento.

### 6.2. Alcance del análisis nutricional

La clasificación se basa en los datos nutricionales disponibles por cada 100 gramos y en las reglas definidas para el azúcar, la sal, las grasas y el Nutri-Score.

Por este motivo:
- El resultado no representa una evaluación completa de todos los aspectos nutricionales del alimento.
- No se tienen necesariamente en cuenta todos los nutrientes, ingredientes, aditivos o características del producto.
- La clasificación no sustituye la lectura del etiquetado nutricional.
- Cuando faltan datos necesarios, puede no ser posible emitir un veredicto fiable.

### 6.3. Limitaciones del Nutri-Score

El Nutri-Score puede no estar disponible para todos los productos. En esos casos, SnackCheck utiliza la clasificación basada en los nutrientes analizados, siempre que exista información suficiente.

La ausencia de Nutri-Score no implica que un producto sea saludable ni que sea perjudicial.

### 6.4. Alcance del proyecto

Los resultados dependen de la configuración actual del flujo de automatización en n8n. La aplicación solo puede procesar los datos y ofrecer las respuestas que permiten los nodos y servicios integrados.


