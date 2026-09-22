# Fase D | Mapa actual de responsabilidades

| Fragmento | Responsabilidad observada | Clase actual |
|---|---|---|
| Validar null/correo/periodo/anticipación | Validación | ServicioReservas |
| Calcular total y descuento VIP | Cálculo de tarifa | ServicioReservas |
| Imprimir "Guardando reserva" | Persistencia simulada | ServicioReservas |
| Imprimir "Correo enviado" | Notificación simulada | ServicioReservas |
| Cambiar estado a CONFIRMADA | Cambio de estado de dominio | Reserva |

## Mapa conceptual

```text
ServicioReservas
├── valida entrada
├── interpreta correo
├── interpreta periodo
├── decide anticipación
├── calcula precio
├── conoce descuento VIP
├── simula persistencia
├── simula notificación
└── ordena confirmar Reserva

Reserva
└── mantiene estado
```

**Pregunta clave:** ¿cuántas razones diferentes podría tener `ServicioReservas` para cambiar?

Al menos **cinco**, cada una independiente de las demás (violación directa del Principio de Responsabilidad Única):

1. Cambia la regla de validación de correo (p. ej. exigir dominio institucional `@uees.edu.ec`).
2. Cambia la regla de periodo válido (p. ej. exigir duración mínima o máxima, no solo `fin > inicio`).
3. Cambia la política de anticipación mínima (p. ej. de 2h a 24h, o distinta según el tipo).
4. Cambia la tarifa base o el descuento VIP (p. ej. nuevos tipos de reserva, porcentaje distinto).
5. Cambia el mecanismo de persistencia (p. ej. guardar en base de datos en vez de imprimir en consola).
6. Cambia el mecanismo de notificación (p. ej. enviar correo real por SMTP en vez de imprimir).

Cualquiera de estos seis motivos obliga a modificar el mismo método `procesar()`, que mezcla validación, tarifa, persistencia y notificación en un solo bloque secuencial sin fronteras internas.
