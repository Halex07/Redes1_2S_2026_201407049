# Manual Técnico — Diseño de Infraestructura Física de Red
## QuetzalDev S.A.

**Redes de Computadoras 1 — Práctica 1**
**Carnet:** 201407049
**Nombre:** Henry Alexander García Montúfar

---

## 1. Inventario de equipos

| Elemento | Cantidad | Ubicación |
|---|---|---|
| Switch principal (core, 16 puertos gestionado L3) | 1 | MDF (Vestíbulo de Ingreso) |
| Patch panel troncal (12 puertos) | 1 | MDF |
| Switch departamental 8 puertos | 4 | Dirección General, Recepción, Legal, Data Center |
| Switch departamental 16 puertos | 4 | RR.HH., Sala de Capacitación, Diseño e Innovación, Backend |
| Patch panel local 8 puertos | 4 | Dirección General, Recepción, Legal, Data Center |
| Patch panel local 12 puertos | 4 | RR.HH., Sala de Capacitación, Diseño e Innovación, Backend |
| Gabinete de pared 12U | 1 | MDF |
| UPS 1000 VA / 600 W | 1 | MDF |
| Tomas de red (unitaria/doble/N puertos) | 48 puntos | Según tabla sección 3 |
| Cable UTP Cat6 (bobina 305 m) | 3 | Bodega / instalación |

**Total de puntos de red del edificio:** 48 (30 PC de escritorio + 12 laptops + 6 servidores).

---

## 2. Ubicación y justificación del MDF

El cuarto de telecomunicaciones (MDF) se ubica en el **Vestíbulo de Ingreso (4 m × 8 m)**, en el punto donde este conecta con el Hall Central.

**Justificación:**
- Es el punto más central del edificio en el eje horizontal (26 m de ancho total), casi equidistante entre el extremo izquierdo (Dirección General) y el derecho (Data Center).
- Tiene acceso directo al Hall Central (26 m × 2 m), que actúa como corredor troncal natural hacia los 8 departamentos, evitando cruzar oficinas.
- Al estar junto al ingreso, facilita el mantenimiento del equipo activo sin interrumpir la operación de ningún departamento.
- Minimiza la distancia promedio de cableado troncal frente a otras ubicaciones posibles (por ejemplo, colocarlo en un extremo del edificio duplicaría la distancia hacia el lado opuesto).

---

## 3. Tomas de red por punto de conexión

| Departamento | Tipo de toma | Cantidad de tomas |
|---|---|---|
| Dirección General | Unitaria | 4 |
| Recepción | Unitaria (3) + toma dedicada servidor | 4 |
| Legal | Unitaria | 4 |
| Data Center | N puertos (rack de servidores, patch cords directos) | 3 |
| RR.HH. | Unitaria | 8 |
| Sala de Capacitación | Doble (mesas compartidas de 2 equipos) | 5 tomas dobles (10 puntos) |
| Diseño e Innovación | Unitaria (7) + toma dedicada servidor | 8 |
| Backend | Unitaria (6) + toma dedicada servidor | 7 |

---

## 4. Justificación de topología física por departamento

| Departamento | Topología | Justificación |
|---|---|---|
| Dirección General | Estrella | Bajo número de hosts (4), sin criticidad especial; estrella simple minimiza costo y facilita el diagnóstico de fallas. |
| Recepción | Estrella | Incluye 1 servidor de bajo tráfico; estrella es suficiente y económica, no se justifica redundancia. |
| Legal | Estrella | Segmento pequeño (4 hosts), documentos sensibles pero bajo volumen de tráfico; estrella estándar. |
| Data Center | Estrella con enlace redundante al core (parcial malla) | Aloja los 3 servidores principales del edificio; requiere mayor tolerancia a fallos, por lo que se recomienda un segundo enlace troncal de respaldo hacia el MDF. |
| RR.HH. | Estrella | 8 hosts, tráfico administrativo estándar; estrella ofrece buena relación costo/escalabilidad. |
| Sala de Capacitación | Estrella | Mayor densidad de hosts (10), pero uso esporádico (capacitaciones); estrella con switch de 16 puertos cubre la demanda y deja margen de crecimiento. |
| Diseño e Innovación | Estrella | Incluye estaciones de trabajo pesadas (diseño/QA) y 1 servidor; estrella con switch de mayor capacidad de puertos y ancho de banda. |
| Backend | Estrella | Incluye 1 servidor de aplicaciones; estrella estándar, con posibilidad de enlace de mayor capacidad (ver sección 6). |

La topología general del edificio (MDF → switches departamentales) es una **estrella extendida (árbol)**, con el MDF como nodo raíz.

---

## 5. Medio de transmisión y categoría de cable por segmento

| Segmento | Medio | Categoría/Tipo | Justificación |
|---|---|---|---|
| Horizontal (todos los deptos., host↔switch) | Cobre UTP | Cat6 | Distancias menores a 20 m, soporta 1 Gbps/10 Gbps a corto alcance; costo-efectivo frente a Cat6a. |
| Troncal → deptos. estándar (Dirección, Recepción, Legal, RR.HH., Capacitación) | Cobre UTP | Cat6 | Distancias entre 6 y 16 m, dentro del límite de 100 m; uplink de 1 Gbps es suficiente para el tráfico agregado de cada switch. |
| Troncal → Diseño e Innovación / Backend | Cobre UTP | Cat6a | Mayor ancho de banda requerido por estaciones de diseño/QA y el servidor de aplicaciones; Cat6a soporta 10 Gbps. |
| Troncal → Data Center | Fibra óptica multimodo (OM3) | Fibra | Enlace crítico de servidores, mayor distancia relativa (~25 m vía corredor secundario) y necesidad de máximo ancho de banda y baja latencia; la fibra también facilita la futura expansión del Data Center. |

---

## 6. Cableado horizontal — distancias y bobinas

*Distancias estimadas sobre el plano (escala 26 m de ancho total), tomando el switch de cada departamento como punto central de referencia y agregando 6 m de holgura por normativa TIA/EIA (slack, terminaciones y tendido vertical).*

| Departamento | Hosts | Distancia media por cable (aprox.) | Metros totales estimados |
|---|---|---|---|
| Dirección General | 4 | 10 m | 40 m |
| Recepción | 4 | 13 m | 52 m |
| Legal | 4 | 10 m | 40 m |
| Data Center | 3 | 9 m | 27 m |
| RR.HH. | 8 | 11 m | 88 m |
| Sala de Capacitación | 10 | 11 m | 110 m |
| Diseño e Innovación | 8 | 12 m | 96 m |
| Backend | 7 | 12 m | 84 m |
| **Subtotal horizontal** | 48 | — | **537 m** |

## 7. Cableado troncal — distancias

| Departamento | Distancia estimada MDF ↔ switch |
|---|---|
| Recepción | 6 m |
| Sala de Capacitación | 8 m |
| Diseño e Innovación | 8 m |
| RR.HH. | 13 m |
| Dirección General | 14 m |
| Backend | 14 m |
| Legal | 16 m |
| Data Center | 25 m |
| **Subtotal troncal** | **104 m** |

## 8. Cálculo de bobinas requeridas

- Total de cable (horizontal + troncal): 537 m + 104 m = **641 m**
- Bobina estándar: 305 m
- 641 m ÷ 305 m = 2.10 → **se requieren 3 bobinas** (redondeo hacia arriba, considerando desperdicio de poncheo y pruebas de certificación).


---

## 9. Dimensionamiento de patch panel y switches

| Ubicación | Puntos a cubrir | Patch panel | Switch |
|---|---|---|---|
| MDF (troncal) | 8 uplinks departamentales | 12 puertos | 16 puertos gestionado (core) |
| Dirección General | 4 | 8 puertos | 8 puertos |
| Recepción | 4 | 8 puertos | 8 puertos |
| Legal | 4 | 8 puertos | 8 puertos |
| Data Center | 3 | 8 puertos | 8 puertos |
| RR.HH. | 8 | 12 puertos | 16 puertos |
| Sala de Capacitación | 10 | 12 puertos | 16 puertos |
| Diseño e Innovación | 8 | 12 puertos | 16 puertos |
| Backend | 7 | 12 puertos | 16 puertos |

En todos los casos el switch seleccionado tiene una cantidad de puertos igual o mayor al patch panel correspondiente, dejando margen de crecimiento (regla del enunciado: patch panel dimensionado a la demanda real, switch ≥ patch panel).

**Justificación de cada elemento:**
- **Switch principal (core):** concentra los 8 enlaces troncales y aplica políticas de VLAN/enrutamiento entre departamentos.
- **Patch panel troncal:** punto de terminación ordenado de los cables que salen del MDF hacia cada departamento, evita empalmes directos sobre el switch.
- **Switches departamentales:** distribuyen la conexión hacia los hosts finales de cada área.
- **Patch panels locales:** terminan el cableado horizontal antes de conectarse (por patch cord) al switch del departamento, facilitando cambios y mantenimiento sin tocar el cableado fijo.

---

## 10. Canalización

| Segmento | Tipo de canalización | Justificación |
|---|---|---|
| Troncal (Hall Central) | Escalerilla metálica abierta (cable tray) | Corredor de uso exclusivo para cableado, con buena ventilación y fácil acceso para futuras ampliaciones; no requiere protección adicional. |
| Horizontal (dentro de cada departamento) | Escalerilla metálica cerrada / canaleta | Corre por espacios de trabajo con mayor tránsito de personas y mobiliario; la canalización cerrada protege el cable de daño físico y de interferencia electromagnética de equipos de oficina. |
| Data Center (fibra) | Bandeja/canaleta dedicada con separación de cobre | La fibra óptica requiere radio de curvatura controlado y separación de fuentes de interferencia; se aísla en su propia bandeja. |

---

## 11. Rack / gabinete

**Propuesta:** gabinete de pared de 12U para el MDF.

**Justificación:** el equipo activo total del MDF (switch core, patch panel troncal, organizador de cables y UPS pequeño) no supera las 8U de ocupación real; un gabinete de pared es más económico que un rack de piso y es suficiente dado que no se proyecta un crecimiento masivo de equipo centralizado (el crecimiento se da a nivel de switches departamentales, no en el MDF).

---

## 12. Estimación de consumo eléctrico y UPS

| Equipo | Consumo aprox. | Cantidad | Subtotal |
|---|---|---|---|
| Switch core (16 puertos gestionado) | 50 W | 1 | 50 W |
| Switch departamental 8 puertos | 15 W | 4 | 60 W |
| Switch departamental 16 puertos | 30 W | 4 | 120 W |
| **Total estimado** | | | **230 W** |

Aplicando un margen de seguridad del 30% (crecimiento y picos de arranque): 230 W × 1.3 ≈ **300 W**.

Convirtiendo a VA con factor de potencia típico de 0.6 (UPS offline/line-interactive estándar): 300 W ÷ 0.6 ≈ 500 VA.

**UPS recomendado: 1000 VA / 600 W**, para dejar margen de autonomía (15–30 min) y espacio de crecimiento futuro sin sobrecargar el equipo.

---

## 13. Estándares T568A / T568B — straight-through y crossover

| Enlace | Tipo de cable | Estándar (ambos extremos) | Justificación |
|---|---|---|---|
| Host (PC/laptop/servidor) ↔ Switch departamental | Straight-through | T568B en ambos extremos | Dispositivos de distinto tipo (host y switch); cada uno usa su propio esquema de transmisión/recepción, por lo que no requieren inversión de pares. |
| Toma de red ↔ Patch panel (horizontal) | Straight-through | T568B en ambos extremos | Es una extensión pasiva del mismo enlace host-switch; debe poncharse bajo el mismo estándar en ambos extremos. |
| Switch departamental ↔ Switch principal (troncal, cobre) | Crossover | T568A en un extremo / T568B en el otro | Se conectan dos dispositivos del mismo tipo (switch-switch); sin auto-MDIX, es necesario invertir los pares de transmisión y recepción para que coincidan correctamente. |

### Disposición de pines — Straight-through (T568B en ambos extremos)

| Pin | Color |
|---|---|
| 1 | Blanco/Naranja |
| 2 | Naranja |
| 3 | Blanco/Verde |
| 4 | Azul |
| 5 | Blanco/Azul |
| 6 | Verde |
| 7 | Blanco/Café |
| 8 | Café |

### Disposición de pines — Crossover (extremo A: T568A / extremo B: T568B)

**Extremo A (T568A):**

| Pin | Color |
|---|---|
| 1 | Blanco/Verde |
| 2 | Verde |
| 3 | Blanco/Naranja |
| 4 | Azul |
| 5 | Blanco/Azul |
| 6 | Naranja |
| 7 | Blanco/Café |
| 8 | Café |

**Extremo B (T568B):** igual a la tabla de la sección anterior.

---

## 14. Etiquetado de cableado

| Cable | Etiqueta |
|---|---|
| Horizontal, ejemplo Recepción punto 1 | `Recepcion-PR01` |
| Horizontal, ejemplo Legal punto 3 | `Legal-PR03` |
| Troncal, ejemplo hacia Recepción | `MDF-Recepcion` |
| Troncal, ejemplo hacia Backend | `MDF-Backend` |

*(Se debe generar una fila de esta tabla por cada uno de los 48 puntos horizontales y los 8 enlaces troncales; aquí se muestran ejemplos representativos.)*

### Comparación con el estándar TIA/EIA-606

El formato de etiquetado usado en esta práctica (`[Depto]-PR0X` y `MDF-[Depto]`) es una simplificación pensada solo para identificar origen y destino de cada cable dentro del ejercicio académico.

El estándar **TIA/EIA-606** (Administración de Infraestructura de Telecomunicaciones) exige un esquema mucho más completo, con al menos estas diferencias frente a lo usado aquí:

1. **Identificadores únicos jerárquicos:** 606 requiere codificar también el edificio, la planta y el gabinete/rack específico dentro del identificador (por ejemplo `EDIF01-P1-MDF-A03`), no solo el nombre del departamento. Nuestro esquema asume un único edificio de un piso y omite esos niveles.
2. **Registro documental formal:** 606 exige mantener registros administrados de espacios, rutas de cableado, terminaciones y propietarios de cada enlace en una base de datos o software de gestión, con control de cambios. En esta práctica el "registro" es simplemente la tabla del manual técnico, sin control de versiones ni administración continua.

**¿Por qué en un entorno real se preferiría el estándar completo?** Porque en un data center o cuarto de telecomunicaciones en producción, con cientos o miles de puntos y múltiples técnicos interviniendo a lo largo de los años, un esquema simplificado como el de esta práctica se vuelve ambiguo y propenso a errores de mantenimiento (cables mal identificados, duplicados o sin dueño claro). El estándar TIA/EIA-606 evita esa ambigüedad al forzar trazabilidad completa y única de cada elemento de la infraestructura.

---

## 15. Flujo de conexión end-to-end

Ejemplo: una laptop del departamento de Backend accede al servidor de Diseño e Innovación.

1. La laptop se conecta mediante cable straight-through (Cat6) a la toma de red `Backend-PR05`.
2. Esa toma corre por cableado horizontal hasta el patch panel local de Backend.
3. Un patch cord conecta el patch panel al switch departamental de Backend.
4. El switch de Backend envía el tráfico por el enlace troncal (`MDF-Backend`, Cat6a) hasta el patch panel troncal en el MDF.
5. El patch panel troncal conecta, mediante patch cord, al switch principal (core) en el MDF.
6. El switch principal enruta el tráfico por el enlace troncal de fibra hacia Diseño e Innovación (o el segmento correspondiente).
7. El switch departamental de Diseño e Innovación recibe el tráfico y lo entrega, por cableado horizontal, hasta la toma de red del servidor destino.

---

## 16. Presupuesto estimado

| Ítem | Cantidad | Precio unitario aprox. (Q) | Subtotal |
|---|---|---|---|
| Switch core 16 puertos gestionado | 1 | Q2,300 | Q2,300 |
| Switch 8 puertos | 4 | Q450 | Q1,800 |
| Switch 16 puertos | 4 | Q1,150 | Q4,600 |
| Patch panel 12 puertos | 5 | Q270 | Q1,350 |
| Patch panel 8 puertos | 4 | Q190 | Q760 |
| Gabinete de pared 12U | 1 | Q1,400 | Q1,400 |
| UPS 1000 VA | 1 | Q1,150 | Q1,150 |
| Bobina UTP Cat6 (305 m) | 2 | Q930 | Q1,860 |
| Bobina UTP Cat6a (305 m) | 1 | Q1,400 | Q1,400 |
| Tramo de fibra OM3 + conectores | 1 lote | Q1,550 | Q1,550 |
| Escalerilla/canaleta (lote) | 1 | Q1,950 | Q1,950 |
| **Total estimado** | | | **≈ Q20,120** |



---

## 17. Consideraciones de escalabilidad futura

- Los switches departamentales de 16 puertos (RR.HH., Capacitación, Diseño, Backend) quedan con 4–9 puertos libres para crecimiento inmediato.
- El patch panel troncal (12 puertos) tiene 4 puertos libres para añadir un noveno segmento sin reconfigurar el MDF.
- El enlace de fibra hacia el Data Center deja margen de ancho de banda para escalar el número de servidores sin cambiar el medio físico.
