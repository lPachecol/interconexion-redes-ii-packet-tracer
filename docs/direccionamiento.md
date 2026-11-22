# Direccionamiento de red

## Redes de acceso

| Sede | VLAN | Área | Subred | Puerta de enlace |
| --- | ---: | --- | --- | --- |
| Guayaquil | 10 | Contabilidad | `192.168.10.0/24` | `192.168.10.1` |
| Guayaquil | 20 | Producción | `192.168.20.0/24` | `192.168.20.1` |
| Guayaquil | 30 | Sistemas | `192.168.30.0/24` | `192.168.30.1` |
| Guayaquil | 150 | Voz | `192.168.150.0/24` | `192.168.150.1` |
| Quito | 40 | Finanzas | `192.168.40.0/24` | `192.168.40.1` |
| Quito | 50 | Producción | `192.168.50.0/24` | `192.168.50.1` |
| Quito | 60 | Sistemas | `192.168.60.0/24` | `192.168.60.1` |
| Quito | 100 | Servidores | `192.168.100.0/24` | `192.168.100.1` |
| Quito | 152 | Voz | `192.168.152.0/24` | `192.168.152.1` |
| Santo Domingo | 151 | Voz | `192.168.151.0/24` | `192.168.151.1` |

Las puertas de enlace de Quito y Guayaquil son virtuales y usan HSRP. En Santo Domingo la dirección pertenece a la subinterfaz del router. La VLAN 100 aloja TFTP en `192.168.100.10` y DHCP en `192.168.100.20`.

## Enlaces enrutados

| Equipo A / interfaz | Dirección A | Equipo B / interfaz | Dirección B |
| --- | --- | --- | --- |
| ROUTER-QUITO / `FastEthernet0/0` | `172.16.1.1/30` | SWITCH-DIST1-UIO / `GigabitEthernet1/0/24` | `172.16.1.2/30` |
| ROUTER-QUITO / `FastEthernet0/1` | `172.16.1.5/30` | SWITCH-DIST2-UIO / `GigabitEthernet1/0/24` | `172.16.1.6/30` |
| ROUTER-GUAYAQUIL / `FastEthernet0/0` | `172.16.2.1/30` | SWITCH-SPINE1-GYE / `GigabitEthernet1/0/24` | `172.16.2.2/30` |
| ROUTER-GUAYAQUIL / `FastEthernet0/1` | `172.16.2.5/30` | SWITCH-SPINE2-GYE / `GigabitEthernet1/0/24` | `172.16.2.6/30` |
| ROUTER-QUITO / `Serial0/0/0` | `10.0.0.1/30` | ROUTER-GUAYAQUIL / `Serial0/0/0` | `10.0.0.2/30` |
| ROUTER-QUITO / `Serial0/0/1` | `10.0.0.9/30` | ROUTER-STO / `Serial0/0/0` | `10.0.0.10/30` |
| ROUTER-GUAYAQUIL / `Serial0/0/1` | `10.0.0.5/30` | ROUTER-STO / `Serial0/0/1` | `10.0.0.6/30` |
| Router0 / `Serial0/0/0` | `200.200.200.1/30` | ROUTER-GUAYAQUIL / `Serial0/1/0` | `200.200.200.2/30` |

El segmento de prueba del ISP tiene un servidor web en `8.8.8.8/24` y un gateway en `8.8.8.1/24`. Estos valores pertenecen exclusivamente al laboratorio.
