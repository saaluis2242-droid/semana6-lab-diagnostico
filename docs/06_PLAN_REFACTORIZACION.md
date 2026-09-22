# Fase L | Plan priorizado de refactorización

No implementes todavía.

| Orden | Cambio | Por qué primero / después | Pruebas requeridas | Dependencias |
|---:|---|---|---|---|
| 1 | Construir la red de seguridad (JUnit 5 + AAA) que caracterice los 6 escenarios de línea base y los casos límite adicionales identificados en la matriz de diagnóstico | Debe ir primero porque **ningún** cambio estructural es seguro sin una prueba automatizada que hoy pase en verde sobre el comportamiento actual. Es la base que hace posible medir si los cambios 2-5 alteran o no el comportamiento observable. | Todas las de `05_PRUEBAS_PROPUESTAS.md`: `vipConservaResultadoActual()`, `unaHoraNoPermiteProcesar()`, `reservaValidaSeConfirma()`, `periodoInvalidoNoProcesa()`, `limiteDeDosHorasEsInclusivo()`, más `correoSinArrobaEsInvalido()` | Ninguna — es el punto de partida (corresponde a "Laboratorio 2") |
| 2 | Separar el cálculo de tarifa (precio base + descuento VIP) en un colaborador propio | Es el cambio de **menor riesgo** de la matriz de riesgo (Bajo): no toca I/O, no cambia el contrato de `Reserva`, y su corrección se verifica comparando un número. Conviene resolverlo primero para reducir el tamaño de `procesar()` antes de tocar las partes más riesgosas. | `vipConservaResultadoActual()` en verde antes y después | Depende del cambio 1 (red de seguridad ya construida) |
| 3 | Separar la notificación (persistencia simulada + envío de correo simulado) del cálculo y de la validación | Riesgo Medio: requiere fijar el orden observable de las acciones (guardar → notificar → confirmar) antes de mover el código, por eso va después de tener pruebas que capturen ese orden exacto. | `reservaValidaSeConfirma()` verificando también el orden/contenido de los mensajes de consola | Depende de 1 y 2 (procesar() ya más pequeño y con el cálculo ya extraído) |
| 4 | Simplificar/reordenar las validaciones de entrada (correo, periodo, anticipación) sin cambiar la regla funcional | Riesgo Alto: es el cambio más tentador de hacer mal porque el orden actual de los `if` determina, ante entradas con varias fallas simultáneas, cuál se "gana". Se deja para el final porque necesita el mayor número de pruebas de caracterización ya construidas (una por condición, de forma aislada) para detectar cualquier alteración. | `unaHoraNoPermiteProcesar()`, `periodoInvalidoNoProcesa()`, `correoSinArrobaEsInvalido()`, y una prueba adicional para `r == null` | Depende de 1, 2 y 3 |
| 5 (fuera de alcance de este laboratorio, queda registrada como siguiente paso) | Introducir los Value Objects `Correo`, `TipoReserva` y `PeriodoReserva` | Es el cambio de mayor riesgo (Alto en la matriz): puede convertir un rechazo silencioso (`return 0`) en una excepción de construcción, lo que **sí** altera el comportamiento observable si no se diseña con cuidado. Debe ser el último porque depende de que los pasos 1-4 ya hayan reducido y aislado la lógica que estos objetos van a reemplazar. | Todas las anteriores, más una decisión explícita y documentada sobre si estos objetos validan en el constructor o no | Depende de 1, 2, 3 y 4 |

## Ejemplo de razonamiento

1. Caracterizar casos actuales.
2. Extraer cálculo a método con intención.
3. Separar notificación / persistencia.
4. Introducir Value Objects.

La evaluación se centra en **la justificación**, no en repetir exactamente este orden.
