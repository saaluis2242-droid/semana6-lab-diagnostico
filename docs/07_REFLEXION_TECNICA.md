# Reflexión técnica final

**¿Qué necesitas comprender y evidenciar antes de modificar una clase heredada que aparentemente funciona?**

Antes de tocar una clase heredada que "funciona" hace falta evidencia, no intuición: una línea base manual que registre entrada, estado y retorno reales para los caminos válidos e inválidos; un mapa de responsabilidades que muestre cuántas razones de cambio conviven en la misma clase; y un diagnóstico concreto de los problemas de diseño, ubicados línea por línea, no descripciones genéricas.

En `ServicioReservas`, el problema de **mayor riesgo** es que las cuatro validaciones retornan el mismo centinela ambiguo (`0`) sin distinguir la causa: cualquier reorganización del orden de esos `if` puede alterar silenciosamente cuál validación "gana" ante una entrada con varias fallas, y hoy no hay ninguna prueba que lo detecte. El problema que **parece fácil pero es engañoso** es introducir Value Objects como `Correo` o `PeriodoReserva`: si validan en su propio constructor, un dato hoy "silenciosamente rechazado" (retorno 0) pasaría a lanzar una excepción al construir la `Reserva`, cambiando el contrato observable aunque la intención sea solo "limpiar tipos".

Las pruebas indispensables antes de tocar código son las que caracterizan cada validación de forma aislada (correo inválido, periodo inválido, anticipación insuficiente) y los dos caminos de tarifa (NORMAL/VIP), incluyendo la frontera exacta de 2 horas.

La primera responsabilidad que movería es el **cálculo de tarifa**, por ser el cambio de menor riesgo (no toca I/O ni el contrato de `Reserva`) y porque reduce el tamaño de `procesar()` antes de abordar validaciones y notificación, que son más riesgosas. Defendería esa decisión con la matriz de riesgo, donde ese cambio queda calificado como Bajo frente al Alto de tocar validaciones o introducir Value Objects.

Finalmente, refactorizar es reorganizar la estructura interna preservando el comportamiento observable (mismos retornos, mismos mensajes, mismos estados); un cambio funcional altera intencionalmente ese comportamiento — por ejemplo, cambiar el porcentaje del descuento VIP.

## Checklist

- [x] Proyecto base compila y ejecuta.
- [x] Seis escenarios de línea base.
- [x] Mapa de responsabilidades.
- [x] Mínimo cinco problemas diagnosticados.
- [x] Matriz de riesgo.
- [x] Pruebas propuestas.
- [x] Plan priorizado.
- [x] Commit Git del estado inicial.
- [x] Reflexión técnica.
