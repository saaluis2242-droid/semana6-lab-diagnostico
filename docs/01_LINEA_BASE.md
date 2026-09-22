# Fase C | Línea base manual

Completa los seis escenarios **sin refactorizar el diseño**.

Evidencia obtenida ejecutando `ServicioReservas.procesar(...)` sobre instancias reales de `Reserva`, sin modificar ninguna clase heredada (harness temporal `LineaBaseRunner`, fuera del código fuente entregable; salida completa en `evidencias/linea-base-salida.txt`).

| ID | Escenario | Entrada principal | Estado | Retorno | Mensajes / excepción |
|---|---|---|---|---|---|
| LB-01 | NORMAL válida | tipo=NORMAL, correo=carlos@uees.edu.ec, inicio/fin válidos (1h), horasAnticipacion=5 | CONFIRMADA | 40.0 | `Guardando reserva R-101` / `Correo enviado a carlos@uees.edu.ec` |
| LB-02 | VIP válida | tipo=VIP, correo=ana@uees.edu.ec, inicio/fin válidos (1h), horasAnticipacion=5 | CONFIRMADA | 34.0 (40 × 0.85) | `Guardando reserva R-102` / `Correo enviado a ana@uees.edu.ec` |
| LB-03 | Correo inválido | correo="carlos-uees.edu.ec" (sin "@"), resto válido, horasAnticipacion=5 | PENDIENTE (sin cambio) | 0.0 | Ninguno — no se imprime nada, no se llama a `confirmar()` |
| LB-04 | Periodo inválido | fin == inicio (no es posterior), resto válido, horasAnticipacion=5 | PENDIENTE (sin cambio) | 0.0 | Ninguno |
| LB-05 | Límite válido | horasAnticipacion=2 (frontera exacta), resto válido | CONFIRMADA | 40.0 | `Guardando reserva R-105` / `Correo enviado a carlos@uees.edu.ec` |
| LB-06 | Límite inválido | horasAnticipacion=1, resto válido | PENDIENTE (sin cambio) | 0.0 | Ninguno |

## Preguntas

1. **¿Qué valores cambian entre NORMAL y VIP?**
   Solo el monto retornado: VIP aplica un factor `0.85` sobre el precio base fijo de `40` (queda en `34.0`), mientras NORMAL retorna el precio base sin descuento (`40.0`). Ningún otro comportamiento observable (mensajes impresos, estado final) cambia entre ambos tipos.

2. **¿Qué casos dejan la reserva en PENDIENTE?**
   Los cuatro casos de validación fallida: `r == null`, correo nulo o sin "@" (LB-03), periodo donde `fin` no es estrictamente posterior a `inicio` (LB-04), y `horasAnticipacion < 2` (LB-06). En todos estos, `confirmar()` nunca se invoca porque el método retorna antes (guard clause con `return 0`), de modo que el objeto `Reserva` conserva el estado con el que fue construido (`PENDIENTE` por defecto).

3. **¿Qué devuelve `procesar()` cuando una entrada no es procesable?**
   Siempre `0` (double `0.0`), sin distinguir *cuál* de las cuatro validaciones falló. El valor `0.0` es ambiguo: es indistinguible de "el servicio cobró cero" si en el futuro existiera una tarifa promocional de $0. No hay ningún tipo de resultado ni excepción que indique la causa del rechazo.

4. **¿Existe alguna excepción visible en el flujo actual?**
   No. El harness envolvió cada llamada en `try/catch` y ninguno de los seis escenarios lanzó una excepción; todas las rutas de error se resuelven con `return 0` silencioso. Esto es consistente con la lectura del código: no hay ningún `throw` en `ServicioReservas`.

5. **¿Qué mensajes aparecen solo cuando la reserva se confirma?**
   `"Guardando reserva " + id` y `"Correo enviado a " + correo`. Ambos se imprimen únicamente en el camino feliz (después de pasar las cuatro validaciones), justo antes de llamar a `r.confirmar()`. Son efectos de consola que simulan persistencia y notificación, acoplados directamente al cálculo de la tarifa dentro del mismo método.
