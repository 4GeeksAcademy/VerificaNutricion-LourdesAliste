# Registro de pruebas

Las pruebas se identifican mediante un código único con el formato
TC-XXX. Se documentan el escenario, el resultado esperado y el resultado obtenido durante la ejecución.

| ID     | Tipo       | Prueba                                                    | Resultado esperado                                            | Resultado obtenido        |
|--------|------------|-----------------------------------------------------------|---------------------------------------------------------------|---------------------------|
| TC-001 | Funcional  | Consultar un producto válido                              | Veredicto coherente con los datos nutricionales               | El esperado               |
| TC-002 | Integración| Consultar Open Food Facts con un código existente         | La API devuelve los datos del producto y el flujo los procesa | El esperado               |
| TC-003 | Error      | Introducir un código desconocido                          | Se informa de que el producto no se ha encontrado             | codigo 404 + json esperado|
| TC-004 | Error      | Introducir un código con formato incorrecto               | Se rechaza la entrada o se informa del formato no válido      | codigo 400 + json esperado|
| TC-005 | Error      | Enviar una petición con el cuerpo vacío                   | Se devuelve un mensaje indicando que falta el código          | codigo 400 + json esperado|
| TC-006 | Error      | Consultar un producto sin datos nutricionales suficientes | No se genera un veredicto nutricional sin fundamento          | codigo 400 + json esperado|