# Guía Completa de Configuración de Redes Cisco

## 1. Introducción

En los dispositivos Cisco, la configuración se realiza principalmente desde la **CLI (Command Line Interface)** utilizando distintos modos de configuración.

### Modos principales

```bash
Router> enable
Router# configure terminal
Router(config)#
```

| Modo            | Símbolo     | Función                   |
| --------------- | ----------- | ------------------------- |
| User EXEC       | `>`         | Acceso básico             |
| Privileged EXEC | `#`         | Permite ver y administrar |
| Global Config   | `(config)#` | Configuración global      |

---

# 2. Configuración Básica Inicial

## Configuración de hostname

```bash
Router(config)# hostname R1
```

---

## Configuración de contraseñas

### Contraseña del modo privilegiado

```bash
R1(config)# enable secret cisco123
```

---

### Contraseña de consola

```bash
R1(config)# line console 0
R1(config-line)# password consola123
R1(config-line)# login
R1(config-line)# exit
```

---

### Configuración de usuario local

```bash
R1(config)# username admin secret admin123
```

---

### Acceso remoto por Telnet

```bash
R1(config)# line vty 0 4
R1(config-line)# login local
R1(config-line)# transport input telnet
R1(config-line)# exit
```

---

### Encriptar contraseñas

```bash
R1(config)# service password-encryption
```

---

### Banner de advertencia

```bash
R1(config)# banner motd # ACCESO SOLO AUTORIZADO #
```

---

### Guardar configuración

```bash
R1# copy running-config startup-config
```

---

# 3. Configuración de Interfaces

## Asignar IP a una interfaz

```bash
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
```

---

## Verificar interfaces

```bash
R1# show ip interface brief
```

---

# 4. Configuración de Switches

## Configuración básica de switch

```bash
Switch> enable
Switch# configure terminal
Switch(config)# hostname SW1
```

---

## Configurar IP de administración

```bash
SW1(config)# interface vlan 1
SW1(config-if)# ip address 192.168.1.2 255.255.255.0
SW1(config-if)# no shutdown
```

---

## Configurar gateway del switch

```bash
SW1(config)# ip default-gateway 192.168.1.1
```

---

# 5. VLANs

## ¿Qué es una VLAN?

Una VLAN permite dividir una red física en múltiples redes lógicas.

Por ejemplo:

| VLAN | Departamento   |
| ---- | -------------- |
| 10   | Administración |
| 20   | Ventas         |
| 30   | TI             |

---

# 6. Crear VLANs

## Crear VLAN

```bash
SW1(config)# vlan 10
SW1(config-vlan)# name ADMIN

SW1(config)# vlan 20
SW1(config-vlan)# name VENTAS
```

---

## Asignar puertos a VLAN

```bash
SW1(config)# interface fastEthernet 0/1
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
```

---

# 7. Trunking

## ¿Qué es un trunk?

Permite transportar múltiples VLANs entre switches o entre switch-router.

---

## Configuración trunk

```bash
SW1(config)# interface gigabitEthernet 0/1
SW1(config-if)# switchport mode trunk
```

---

# 8. Router-on-a-Stick

## ¿Qué es?

Permite enrutar múltiples VLANs usando una sola interfaz física del router.

---

## Configuración ejemplo

### En el switch

```bash
SW1(config)# interface g0/1
SW1(config-if)# switchport mode trunk
```

---

### En el router

```bash
R1(config)# interface g0/0.10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip address 192.168.10.1 255.255.255.0

R1(config)# interface g0/0.20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip address 192.168.20.1 255.255.255.0
```

---

# 9. Enrutamiento Estático

## ¿Qué es?

Las rutas se agregan manualmente.

---

## Sintaxis

```bash
ip route RED_DESTINO MASCARA NEXT_HOP
```

---

## Ejemplo

```bash
R1(config)# ip route 192.168.2.0 255.255.255.0 10.0.0.2
```

Esto indica:

* Para llegar a `192.168.2.0`
* enviar paquetes hacia `10.0.0.2`

---

## Ruta por defecto

```bash
R1(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.2
```

---

# 10. Enrutamiento Dinámico

## ¿Qué es?

Los routers aprenden rutas automáticamente.

---

# 11. RIP

## Configuración

```bash
R1(config)# router rip
R1(config-router)# version 2
R1(config-router)# network 192.168.1.0
R1(config-router)# network 10.0.0.0
R1(config-router)# no auto-summary
```

---

# 12. OSPF

## Configuración básica

```bash
R1(config)# router ospf 1
R1(config-router)# network 192.168.1.0 0.0.0.255 area 0
R1(config-router)# network 10.0.0.0 0.0.0.3 area 0
```

---

## Explicación Wildcard Mask

| Máscara normal | Wildcard    |
| -------------- | ----------- |
| 255.255.255.0  | 0.0.0.255   |
| 255.255.0.0    | 0.0.255.255 |

---

# 13. EIGRP

```bash
R1(config)# router eigrp 100
R1(config-router)# network 192.168.1.0
R1(config-router)# no auto-summary
```

---

# 14. ACL - Access Control Lists

## ¿Qué son?

Permiten filtrar tráfico.

---

# 15. ACL Estándar

## Filtran por IP origen

### Ejemplo

Bloquear red `192.168.1.0`

```bash
R1(config)# access-list 10 deny 192.168.1.0 0.0.0.255
R1(config)# access-list 10 permit any
```

Aplicar ACL:

```bash
R1(config)# interface g0/0
R1(config-if)# ip access-group 10 in
```

---

# 16. ACL Extendida

## Filtran:

* IP origen
* IP destino
* protocolos
* puertos

---

## Ejemplo HTTP

Bloquear HTTP:

```bash
R1(config)# access-list 100 deny tcp any any eq 80
R1(config)# access-list 100 permit ip any any
```

Aplicar:

```bash
R1(config)# interface g0/1
R1(config-if)# ip access-group 100 in
```

---

# 17. ACL Nombradas

## ACL estándar nombrada

```bash
R1(config)# ip access-list standard BLOQUEO
R1(config-std-nacl)# deny 192.168.1.0 0.0.0.255
R1(config-std-nacl)# permit any
```

---

## ACL extendida nombrada

```bash
R1(config)# ip access-list extended WEBFILTER
R1(config-ext-nacl)# deny tcp any any eq 80
R1(config-ext-nacl)# permit ip any any
```

---

# 18. NAT

# ¿Qué es NAT?

Network Address Translation permite convertir IP privadas en públicas.

---

# 19. NAT Estático

## Ejemplo

```bash
R1(config)# ip nat inside source static 192.168.1.10 200.1.1.10
```

---

## Definir interfaces

### LAN

```bash
R1(config)# interface g0/0
R1(config-if)# ip nat inside
```

### WAN

```bash
R1(config)# interface g0/1
R1(config-if)# ip nat outside
```

---

# 20. NAT Dinámico

## Crear pool

```bash
R1(config)# ip nat pool PUBLICAS 200.1.1.1 200.1.1.10 netmask 255.255.255.0
```

---

## ACL para NAT

```bash
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255
```

---

## Asociar pool

```bash
R1(config)# ip nat inside source list 1 pool PUBLICAS
```

---

# 21. PAT (NAT Overload)

## El más usado para Internet

```bash
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255

R1(config)# ip nat inside source list 1 interface g0/1 overload
```

---

# 22. DHCP en Router Cisco

## Configuración

```bash
R1(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10

R1(config)# ip dhcp pool LAN
R1(dhcp-config)# network 192.168.1.0 255.255.255.0
R1(dhcp-config)# default-router 192.168.1.1
R1(dhcp-config)# dns-server 8.8.8.8
```

---

# 23. Verificación y Troubleshooting

## Ver rutas

```bash
show ip route
```

---

## Ver VLANs

```bash
show vlan brief
```

---

## Ver trunk

```bash
show interfaces trunk
```

---

## Ver ACLs

```bash
show access-lists
```

---

## Ver NAT

```bash
show ip nat translations
show ip nat statistics
```

---

## Ver vecinos OSPF

```bash
show ip ospf neighbor
```

---

## Ver vecinos EIGRP

```bash
show ip eigrp neighbors
```

---

# 24. Ejemplo Completo de Red

## Escenario

* VLAN 10 → 192.168.10.0/24
* VLAN 20 → 192.168.20.0/24
* Router conectado al ISP
* NAT habilitado
* OSPF funcionando

---

## Switch

```bash
enable
configure terminal

vlan 10
name ADMIN

vlan 20
name VENTAS

interface f0/1
switchport mode access
switchport access vlan 10

interface f0/2
switchport mode access
switchport access vlan 20

interface g0/1
switchport mode trunk
```

---

## Router

```bash
enable
configure terminal

hostname R1

enable secret cisco123

username admin secret admin123

line console 0
password consola123
login

line vty 0 4
login local
transport input telnet

interface g0/0
no shutdown

interface g0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0

interface g0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0

interface g0/1
ip address 200.1.1.2 255.255.255.252
ip nat outside
no shutdown

interface g0/0
ip nat inside

access-list 1 permit 192.168.10.0 0.0.0.255
access-list 1 permit 192.168.20.0 0.0.0.255

ip nat inside source list 1 interface g0/1 overload

router ospf 1
network 192.168.10.0 0.0.0.255 area 0
network 192.168.20.0 0.0.0.255 area 0

ip route 0.0.0.0 0.0.0.0 200.1.1.1
```

---

# 25. Recomendaciones Finales

## Buenas prácticas

* Usar nombres descriptivos para VLANs
* Documentar direccionamiento IP
* Usar SSH en lugar de Telnet en producción
* Guardar configuración frecuentemente
* Usar ACLs con cuidado
* Separar tráfico por VLANs
* Verificar rutas antes de implementar NAT

---

# 26. Configuración SSH (Recomendado sobre Telnet)

```bash
R1(config)# ip domain-name empresa.local

R1(config)# crypto key generate rsa
1024

R1(config)# username admin secret admin123

R1(config)# line vty 0 4
R1(config-line)# login local
R1(config-line)# transport input ssh
```

---

# 27. Comandos Más Utilizados

| Comando                  | Función                    |
| ------------------------ | -------------------------- |
| show running-config      | Ver configuración actual   |
| show startup-config      | Ver configuración guardada |
| copy run start           | Guardar configuración      |
| show ip route            | Tabla de rutas             |
| show vlan brief          | Ver VLANs                  |
| show interfaces trunk    | Ver trunk                  |
| show access-lists        | Ver ACL                    |
| show ip nat translations | Ver NAT                    |
| ping                     | Probar conectividad        |
| traceroute               | Ver ruta de paquetes       |

---

# 28. Flujo General de Configuración

## Paso 1

Configurar:

* hostname
* passwords
* interfaces

---

## Paso 2

Crear VLANs según necesidad.

Si existen:

* 2 VLANs → crear 2 subinterfaces
* 5 VLANs → crear 5 subinterfaces

Cada VLAN necesita:

* red diferente
* gateway diferente

---

## Paso 3

Configurar routing:

* Estático → redes pequeñas
* OSPF/EIGRP → redes medianas/grandes

---

## Paso 4

Aplicar ACLs según políticas.

---

## Paso 5

Configurar NAT para salida a Internet.

---

# 29. Ejemplo de Diseño Lógico

| VLAN | Red             | Gateway      |
| ---- | --------------- | ------------ |
| 10   | 192.168.10.0/24 | 192.168.10.1 |
| 20   | 192.168.20.0/24 | 192.168.20.1 |
| 30   | 192.168.30.0/24 | 192.168.30.1 |

Cada VLAN:

* tiene su propia red
* necesita gateway
* puede tener ACLs independientes
* puede tener DHCP independiente
