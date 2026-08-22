# Informe de Desarrollo — Práctica 1
## QuetzalDev S.A.

**Nombre:** Henry Alexander García Montúfar	
**Carnet:** 201407049

---

## 1. Proceso de diseño

Para abordar esta práctica partí del plano arquitectónico proporcionado (edificio de un solo nivel, 26 m × 16 m, con una extensión de 6 m × 4 m para el Data Center). El primer paso fue interpretar la distribución de los ocho departamentos y confirmar que la suma de dispositivos coincidiera con lo solicitado en el enunciado: 30 computadoras de escritorio, 12 laptops y 6 servidores, para un total de 48 puntos de red.

Antes de dibujar cualquier cable, definí la ubicación del **cuarto de telecomunicaciones (MDF)**. Evalué mentalmente dos alternativas: colocarlo en un extremo del edificio (por ejemplo, junto a Dirección General) o colocarlo en el Vestíbulo de Ingreso, en el punto donde este conecta con el Hall Central. Descarté la primera opción porque hubiera duplicado la distancia de cableado troncal hacia los departamentos del extremo opuesto (Backend y Data Center). El Vestíbulo resultó ser el punto que minimiza la distancia promedio hacia los ocho segmentos, además de tener acceso directo al Hall Central, que funciona como corredor natural para el cableado troncal sin necesidad de atravesar oficinas.

## 2. Criterios para la selección de topología

Para cada departamento evalué tres factores: número de hosts, criticidad del segmento y el balance costo/escalabilidad/tolerancia a fallos, tal como pedía el enunciado.

En la mayoría de los departamentos (Dirección General, Recepción, Legal, RR.HH., Sala de Capacitación) el número de hosts es bajo o moderado y el tráfico no es crítico, por lo que una **topología en estrella simple** resultó ser la opción más costo-efectiva: es fácil de diagnosticar, escalar y no introduce complejidad innecesaria.

El caso especial fue el **Data Center**, que concentra los tres servidores principales del edificio. Ahí sí consideré necesario reforzar la tolerancia a fallos, por lo que propuse un enlace troncal redundante hacia el MDF, adicional a la conexión en estrella base. Este fue el único segmento donde el balance costo/beneficio justificó apartarse de la estrella pura.

## 3. Selección de medios de transmisión

Para el cableado horizontal (switch de departamento ↔ hosts) elegí **UTP Cat6** de forma generalizada, ya que ninguna distancia horizontal supera los 20 metros aproximados y Cat6 ofrece suficiente ancho de banda (hasta 1 Gbps a 100 m, o 10 Gbps a distancias cortas) a un costo razonable frente a Cat6a.

Para el cableado troncal apliqué un criterio diferenciado por segmento:
- **Cat6** para los departamentos con demanda de tráfico estándar (Dirección, Recepción, Legal, RR.HH., Sala de Capacitación).
- **Cat6a** para Diseño e Innovación y Backend, porque agrupan estaciones de trabajo más exigentes (diseño gráfico, QA, aplicaciones de backend) y un servidor departamental cada uno.
- **Fibra óptica multimodo (OM3)** exclusivamente para el enlace hacia el Data Center, dado que es el segmento más crítico del edificio (concentra los servidores principales), tiene la mayor distancia relativa dentro del edificio, y se beneficia de mayor ancho de banda y menor latencia a futuro.

## 4. Retos de planificación física al interpretar el plano

El mayor reto fue calcular las distancias de cableado sin contar con medidas exactas punto por punto, solo con la escala general del plano (26 m de ancho, 16 m de alto). Para resolverlo, tomé el switch de cada departamento como punto de referencia central y estimé la distancia hacia el MDF siguiendo la ruta física real (a través del Hall Central y, en el caso del Data Center, del corredor secundario de 1.5 m), en lugar de usar la distancia en línea recta, que hubiera subestimado el cableado necesario.

Otro reto fue decidir cómo representar visualmente, sobre un plano pensado originalmente para mobiliario y no para redes, la diferencia entre cableado troncal y horizontal sin que el diagrama se saturara. Opté por usar una franja de canalización a lo largo del Hall Central (color y grosor distintos para troncal, fibra y horizontal) y agrupar las tomas de red de cada departamento en una fila cercana al switch, en lugar de trazar las 48 líneas individuales completas, lo cual habría hecho el diagrama ilegible a nivel de plano completo.

## 5. Justificación del medio troncal

La decisión de usar tres tipos distintos de medio en el cableado troncal (Cat6, Cat6a y fibra) responde a un principio de costo-beneficio: no tiene sentido instalar fibra óptica en todos los enlaces troncales cuando la mayoría de los departamentos genera un volumen de tráfico bajo o moderado, que Cat6 cubre sin problema y a menor costo. Sin embargo, reservar la fibra para el Data Center sí se justifica porque es el único segmento donde una falla o un cuello de botella afecta a toda la operación de la empresa (los tres servidores principales), y porque la fibra deja mayor margen de crecimiento futuro sin tener que volver a cablear ese tramo.

## 6. Conclusiones

Este ejercicio me permitió aplicar los principios de cableado estructurado y diseño de Capa 1 antes de entrar a configuración lógica: definir dónde ubicar el cuarto de telecomunicaciones, cómo dimensionar el equipo activo según la demanda real, y cómo justificar cada decisión de medio físico en función de distancia, ancho de banda y criticidad del segmento, en lugar de aplicar una solución única a todo el edificio.
