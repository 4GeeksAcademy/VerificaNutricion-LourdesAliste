# Changelog
 
Todos los cambios relevantes de SnackCheck se documentan en este archivo siguiendo los principios de Semantic Versioning (SemVer).

## [1.0.0] - 2026-10-15
  
### Añadido
  
- Endpoint webhook POST `/nutricion-check`.
- Validación básica de formato EAN13.
- Consulta a Open Food Facts.
- Detección de productos inexistentes.
- Verificación de disponibilidad de Nutri-Score.
- Respuestas JSON de error.
- Primera versión funcional de SnackCheck.  

### Previsto
- Mejorar el tratamiento de entradas incorrectas y productos sin datos suficientes.


## [1.1.0] - 2026-10-15
  
### Añadido
  
- Nodo dedicado para asignar la variable `barcode`.
- Validación explícita de existencia del código de barras.
- Mejora de las comprobaciones de formato.
  
### Cambiado
  
- Refactorización del flujo de entrada.
- Simplificación de la comunicación entre nodos.
 
  
## [2.0.0] - 2026-10-16
  
### Añadido
  
- Validación avanzada de códigos de barras:
    - inexistente
   - longitud incorrecta
   - valor vacío
   - caracteres no numéricos
  
- Gestión de errores específicos con mensajes personalizados.
  
- Verificación de disponibilidad de información nutricional mínima.
  
- Extracción automática de:
   - nombre del producto
   - marca
   - Nutri-Score
   - azúcar
   - sal
   - grasa
   - calorías
  
- Implementación de sistema de semáforo nutricional.
  
- Cálculo automático de:
   - nivel de azúcar
   - nivel de sal
   - nivel de grasa
   - puntuación de preocupación
 
- Implementación del algoritmo de clasificación nutricional:
   - saludable
   - moderado
   - no saludable
  
- Integración con Groq LLM para generar explicaciones comprensibles para el usuario.
  
  - Generación de informe nutricional enriquecido con:
    - iconografía visual
    - resumen ejecutivo
    - interpretación del resultado
  
  
### Cambiado
  
  - Reestructuración completa del flujo de procesamiento.
  - Separación de validaciones en nodos específicos.
  - Mejora de la robustez frente a respuestas incompletas de Open Food Facts.
  - Respuesta final más descriptiva y orientada al usuario.
  

### Previsto
- Completar y documentar las pruebas funcionales, de integración y de errores.