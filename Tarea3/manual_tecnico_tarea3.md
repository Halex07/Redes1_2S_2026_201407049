# Manual Técnico – Tarea #3: VLANs y VTP

**Universidad San Carlos de Guatemala**
**Facultad de Ingeniería – Escuela de Ingeniería en Ciencias y Sistemas**
**Laboratorio – Redes de Computadoras 1 – A**
**Segundo Semestre 2026**

**Nombre del estudiante:** Henry Alexander García Montúfar 
**Carnet:** 201407049
**Fecha de entrega:** 28/08/2026

---

## 1. Objetivo

Configurar una red en Cisco Packet Tracer implementando VLANs y el protocolo VTP en
diferentes modos de operación (servidor, cliente y transparente), verificando la
propagación correcta de VLANs entre switches y la conectividad entre PCs de la misma VLAN.

---

## 2. Topología de red

> Inserta aquí una captura de pantalla de la topología completa en Packet Tracer
> (4 switches + 6 PCs).

imagen18.png

### 2.1 Puertos reales de la topología

| Enlace | Puerto en Switch0 | Puerto en switch de acceso |
|---|---|---|
| Switch0 ↔ ADMIN | Fa0/1 (trunk) | Fa0/3 (trunk) |
| Switch0 ↔ MERCA | Fa0/2 (trunk) | Fa0/1 (trunk) |
| Switch0 ↔ VENTAS | Fa0/3 (trunk) | Fa0/1 (trunk) |

| Switch de acceso | Puerto acceso PC | PC conectada | VLAN |
|---|---|---|---|
| ADMIN | Fa0/1 | PC0 | 10 |
| ADMIN | Fa0/2 | PC1 | 20 |
| MERCA | Fa0/2 | PC2 | 10 |
| MERCA | Fa0/3 | PC3 | 30 |
| VENTAS | Fa0/2 | PC4 | 20 |
| VENTAS | Fa0/3 | PC5 | 30 |

### 2.2 Direccionamiento IP

| PC | Switch de acceso | VLAN | Dirección IP | Máscara |
|---|---|---|---|---|
| PC0 | ADMIN | 10 (ADMIN) | 192.168.10.11 | 255.255.255.0 |
| PC1 | ADMIN | 20 (MERCA) | 192.168.20.11 | 255.255.255.0 |
| PC2 | MERCA | 10 (ADMIN) | 192.168.10.12 | 255.255.255.0 |
| PC3 | MERCA | 30 (VENTAS) | 192.168.30.11 | 255.255.255.0 |
| PC4 | VENTAS | 20 (MERCA) | 192.168.20.12 | 255.255.255.0 |
| PC5 | VENTAS | 30 (VENTAS) | 192.168.30.12 | 255.255.255.0 |

---

## 3. Configuración de VLANs y VTP

**Dominio VTP usado:** `LABREDES`
**Contraseña VTP usada:** `cisco123`

### 3.1 Switch0 (VTP Servidor)

```
Switch>enable
Switch#configure terminal
Switch(config)#hostname Switch0
Switch0(config)#vtp domain LABREDES
Switch0(config)#vtp mode server
Switch0(config)#vtp password cisco123
Switch0(config)#vlan 10
Switch0(config-vlan)#name ADMIN
Switch0(config-vlan)#exit
Switch0(config)#vlan 20
Switch0(config-vlan)#name MERCA
Switch0(config-vlan)#exit
Switch0(config)#vlan 30
Switch0(config-vlan)#name VENTAS
Switch0(config-vlan)#exit
Switch0(config)#interface fa0/1
Switch0(config-if)#switchport mode trunk
Switch0(config-if)#exit
Switch0(config)#interface fa0/2
Switch0(config-if)#switchport mode trunk
Switch0(config-if)#exit
Switch0(config)#interface fa0/3
Switch0(config-if)#switchport mode trunk
Switch0(config-if)#exit
```

**Verificación `show vtp status`:**

**Verificación `show vlan brief`:**
imagen0.png

### 3.2 Switch ADMIN (VTP Cliente)

```
Switch>enable
Switch#configure terminal
Switch(config)#hostname ADMIN
ADMIN(config)#vtp domain LABREDES
ADMIN(config)#vtp mode client
ADMIN(config)#vtp password cisco123
ADMIN(config)#interface fa0/3
ADMIN(config-if)#switchport mode trunk
ADMIN(config-if)#exit
ADMIN(config)#interface fa0/1
ADMIN(config-if)#switchport mode access
ADMIN(config-if)#switchport access vlan 10
ADMIN(config-if)#exit
ADMIN(config)#interface fa0/2
ADMIN(config-if)#switchport mode access
ADMIN(config-if)#switchport access vlan 20
ADMIN(config-if)#exit
```

**Verificación `show vtp status`:**

**Verificación `show vlan brief`:**
imagen10.png


### 3.3 Switch MERCA (VTP Cliente)

```
Switch>enable
Switch#configure terminal
Switch(config)#hostname MERCA
MERCA(config)#vtp domain LABREDES
MERCA(config)#vtp mode client
MERCA(config)#vtp password cisco123
MERCA(config)#interface fa0/1
MERCA(config-if)#switchport mode trunk
MERCA(config-if)#exit
MERCA(config)#interface fa0/2
MERCA(config-if)#switchport mode access
MERCA(config-if)#switchport access vlan 10
MERCA(config-if)#exit
MERCA(config)#interface fa0/3
MERCA(config-if)#switchport mode access
MERCA(config-if)#switchport access vlan 30
MERCA(config-if)#exit
```

**Verificación `show vtp status`:**

**Verificación `show vlan brief`:**

imagen6.png

### 3.4 Switch VENTAS (VTP Transparente)

```
Switch>enable
Switch#configure terminal
Switch(config)#hostname VENTAS
VENTAS(config)#vtp domain LABREDES
VENTAS(config)#vtp mode transparent
VENTAS(config)#vtp password cisco123
VENTAS(config)#interface fa0/1
VENTAS(config-if)#switchport mode trunk
VENTAS(config-if)#exit
VENTAS(config)#vlan 10
VENTAS(config-vlan)#name ADMIN
VENTAS(config-vlan)#exit
VENTAS(config)#vlan 20
VENTAS(config-vlan)#name MERCA
VENTAS(config-vlan)#exit
VENTAS(config)#vlan 30
VENTAS(config-vlan)#name VENTAS
VENTAS(config-vlan)#exit
VENTAS(config)#interface fa0/2
VENTAS(config-if)#switchport mode access
VENTAS(config-if)#switchport access vlan 20
VENTAS(config-if)#exit
VENTAS(config)#interface fa0/3
VENTAS(config-if)#switchport mode access
VENTAS(config-if)#switchport access vlan 30
VENTAS(config-if)#exit
```

**Verificación `show vtp status`:**

**Verificación `show vlan brief`:**

imagen19.png
---

## 4. Pruebas de conectividad

### 4.1 Ping entre PCs de la misma VLAN (debe responder)

| Origen | Destino | VLAN | Resultado esperado | Resultado obtenido |
|---|---|---|---|---|
| PC0 (192.168.10.11) | PC2 (192.168.10.12) | 10 | Éxito | ✅ Confirmado — 4/4 paquetes recibidos, 0% loss |pc0pc2.png
| PC1 (192.168.20.11) | PC4 (192.168.20.12) | 20 | Éxito |  |pc1pc4.png
| PC3 (192.168.30.11) | PC5 (192.168.30.12) | 30 | Éxito |  |pc3pc5.png

### 4.2 Ping entre PCs de distinta VLAN (debe fallar)

| Origen | Destino | VLANs | Resultado esperado | Resultado obtenido |
|---|---|---|---|---|
| PC0 (192.168.10.11) | PC1 (192.168.20.11) | 10 → 20 | Falla | ✅ Confirmado — Request timed out |pc0pc1.png
| PC2 (192.168.10.12) | PC3 (192.168.30.11) | 10 → 30 | Falla |  |pc2pc43.png
| PC4 (192.168.20.12) | PC5 (192.168.30.12) | 20 → 30 | Falla |  |pc4pc5.png

---

## 5. Conclusiones

La implementación de VTP permitió centralizar la administración de VLANs desde un único switch servidor (Switch0), evitando tener que crear manualmente 
las VLANs 10, 20 y 30 en cada uno de los switches de acceso. Se comprobó que el modo cliente (ADMIN y MERCA) recibe y sincroniza automáticamente
las VLANs del servidor siempre que el dominio VTP, la contraseña y el trunk coincidan correctamente en ambos extremos del enlace, mientras que 
el modo transparente (VENTAS) requiere crear las VLANs de forma local, ya que solo reenvía los anuncios VTP sin aplicarlos. Las pruebas de ping 
confirmaron que las VLANs efectivamente segmentan el tráfico de la red: los dispositivos de una misma VLAN pueden comunicarse entre sí aunque estén 
conectados a switches distintos (gracias a los enlaces trunk),
mientras que dispositivos en VLANs diferentes no logran comunicarse 
aunque compartan el mismo switch físico, evidenciando el aislamiento lógico que proporciona la segmentación por VLAN.
---

## 6. Recomendaciones aplicadas

- Se verificó que los enlaces entre switches estuvieran en modo trunk antes de probar VTP.
- Se utilizó el mismo dominio VTP (`LABREDES`) en todos los switches.
- Se mantuvo el mismo número de revisión VTP para evitar sobrescribir VLANs existentes.
- Se confirmó el número real de puerto de cada enlace (mediante los mensajes CDP y el
  diagrama de Packet Tracer) antes de asignar el modo trunk, ya que el orden de los
  puertos no siempre coincide entre ambos extremos de un mismo cable.
