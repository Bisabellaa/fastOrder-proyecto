# FAST ORDER

Una plataforma móvil que permite al cliente de un restaurante de sushi realizar su pedido desde su celular (escaneando el QR de su mesa) y usar una botonera física en la mesa, y que la orden llegue al instante a la cocina sin intermediarios.

## OBJETIVOS DEL PROYECTO

**Objetivo General:** Desarrollar una aplicación de autogestión de pedidos para clientes mediante una aplicación móvil y una botonera integrada con Arduino (ESP32), permitiendo una atención autónoma, rápida y sin intermediarios manuales en la toma de órdenes.

**Objetivos Específicos:**

- FastOrder es una solución tecnológica que otorga autonomía al cliente para realizar pedidos desde su dispositivo o mediante una interfaz física en la mesa.
- La importancia del proyecto es que resuelve la mala atención del personal, los tiempos de espera excesivos y la confusión al tomar pedidos, permitiendo al cliente solicitar productos sin llamar reiteradamente al mozo.
- Se centra en la interacción directa Cliente-Sistema-Cocina, evaluando la eficiencia de la respuesta automática y la precisión de las órdenes.
- Mejorar en la experiencia del usuario presencial, quien ahora tiene el control total de su tiempo y consumo.
- Adquirir conocimientos sobre el estudio técnico (QA).
- Optimización del personal de salón (quienes ahora se enfocan en la entrega y no en la toma de datos) y aumento de la rotación de mesas por rapidez en el flujo de pedido.

## RESULTADOS ESPERADOS

Se espera que el cliente obtenga una atención eficaz y certera. El sistema debe procesar los pedidos a una velocidad mayor, eliminando errores de interpretación del mozo y permitiendo que la cocina reciba la orden en el instante exacto en que el cliente presiona el botón o confirma en la app.

## BREVE DESCRIPCIÓN DEL PROYECTO

FastOrder evoluciona a un sistema de autoservicio inteligente. Consiste en una aplicación móvil para el cliente y una botonera programada con Arduino (ESP32) fija en la mesa. El cliente escanea el QR de su mesa, realiza el pedido desde su smartphone y lo confirma con el botón físico "Hacer pedido"; además utiliza los botones físicos para solicitudes rápidas ("Llamar mozo", "Pedir cuenta"). La señal viaja al instante al Dashboard de Cocina y al panel del personal de servicio.

La botonera de cada mesa se presenta en la red local como "mesa N online". Un segundo dispositivo ESP32 actúa como llamador inalámbrico del mozo: muestra qué mesa lo llama y por qué motivo mediante pantalla OLED, LEDs de color (rojo = llaman, verde = cuenta, azul = pedido listo) y un buzzer para ambientes ruidosos. Con un botón, el mozo confirma cada aviso y pasa al siguiente; los llamados de varias mesas se atienden en orden de llegada (cola FIFO).

Al presionar "Pedir cuenta", el cliente ve en su celular el detalle y el total de lo consumido, y el mozo es notificado para acercarse a cobrar. El sistema **no emite factura fiscal (sin AFIP)**: solo calcula y muestra el total de lo que el cliente va a consumir, ni más ni menos. Cada aviso y cada cuenta quedan registrados en la base de datos del sistema, permitiendo consultar el historial del día.

## JUSTIFICACIÓN DEL PROYECTO

Es la optimización de la comunicación y quitar los obstáculos que hacen que el cliente pierda tiempo. Al eliminar el paso intermedio de la toma de pedido manual por el mozo, se minimizan las demoras y confusiones, brindando al cliente una sensación de control y rapidez que mejora su experiencia general.

## IDENTIFICACIÓN DE POSIBLES BENEFICIARIOS DE LOS RESULTADOS DEL PROYECTO

- **Clientes:** Beneficiarios directos que disfrutan de una atención rápida y sin interrupciones innecesarias.
- **Personal de Cocina:** Reciben pedidos estandarizados y directos del consumidor final.
- **Dueño del Restaurante de sushi:** Optimiza la labor de sus empleados y cuenta con el registro de llamados y consumos del día (base para futuras métricas reales de consumo).
- **Mozos:** Su rol se transforma de "tomadores de pedidos" a "gestores de experiencia y entrega", reduciendo su carga de trabajo operativa.

## DURACIÓN ESTIMADA DEL PROYECTO

- **Fase de Desarrollo (App Móvil y Dashboard):** 4 semanas. Centrado en la base de datos, el servidor con comunicación en tiempo real (WebSockets), el menú digital y la lógica de pedidos para el cliente.
- **Fase de Integración de Hardware (Arduino):** 3 semanas. Programación de la botonera física, del llamador del mozo y comunicación vía WebSockets por la red local.
- **Fase de Pruebas y Despliegue:** 2 semanas. Control de calidad (QA), pruebas de estrés en salón y puesta en marcha.
- **Duración Total:** Aproximadamente 9 a 10 semanas para la operatividad inicial en el restaurante de sushi.

## DEFINICIÓN DEL EQUIPO DE TRABAJO

- **Isabella Brenda:** Desarrolladora y Líder de Proyecto.

## DOCUMENTOS MENCIONADOS

- **Análisis de Requerimientos, Historias de Usuario y Casos de Uso** — `docs/analisis-fastorder.md`
- **Diagrama de Casos de Uso (draw.io)** — `docs/diagrama-casos-de-uso.drawio`
- **Especificación de Diseño** — `docs/superpowers/specs/2026-09-07-fastorder-design.md`