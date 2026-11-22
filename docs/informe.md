# Diseño de red para Muebles Andinos

## Escenario

El proyecto del segundo bimestre de Redes de Computadores II modela la conectividad corporativa de Muebles Andinos S.A. La matriz de Quito reúne Finanzas, Sistemas, Producción y los servicios centrales. Guayaquil representa la planta y su tráfico entre áreas. Santo Domingo conecta ambos extremos de la WAN y cuenta con una extensión IP. El ISP y un servidor web son parte del laboratorio.

## Arquitecturas de sede

| Sede | Disposición | Justificación |
| --- | --- | --- |
| **Quito** | Router Core, dos switches multicapa de Distribución y switches de Acceso | El Core termina los circuitos WAN. Distribución reúne los gateways de VLAN, aplica la política de acceso a servidores y conecta TFTP y DHCP. Acceso concentra las estaciones y el teléfono, con uplinks a ambos equipos de Distribución. |
| **Guayaquil** | Dos spines multicapa y dos leaves L2 en servicio. Cada leaf se conecta a ambos spines | El doble enlace por leaf ofrece rutas físicas alternativas y permite crecer añadiendo otro switch de acceso al mismo par de agregación. Los spines concentran SVIs y gateways HSRP. Los leaves conmutan los puertos de usuario y voz. |
| **Santo Domingo** | Router de tránsito, switch L2 y segmento de voz | Un router es suficiente para terminar los dos circuitos WAN y dar servicio a la VLAN 151 mediante una subinterfaz. La simulación también conserva un PC de prueba en esta sede. |

Quito y Guayaquil tienen una forma similar al mirar solo sus enlaces redundantes. La distinción de Quito está en el papel de Core, Distribución y Acceso, con servicios propios en Distribución. En Guayaquil, la vista se lee de izquierda a derecha: borde WAN → columna Spine → columna Leaf → áreas usuarias. El cruce entre columnas muestra que cada leaf conecta con ambos spines. La malla se implementa con troncales de Capa 2 y STP. El router WAN sigue separado de la agregación.

En Quito, `SWITCH-BACKUP-UIO` aparece cableado a los dos switches de Distribución y a tres terminales. `SWITCH-ACC2-UIO` aparece sin enlaces. En Guayaquil, el leaf conectado al grupo de Producción es `SWITCH-LEAF2.2-GYE`. La otra unidad, `SWITCH-LEAF2-GYE`, queda sin enlaces. El nombre de un equipo identifica su función prevista. El cableado indica su función efectiva en la simulación.

[Vistas de Quito](../imagenes/quito.png) · [Vistas de Guayaquil](../imagenes/guayaquil.png)

## Enrutamiento y continuidad

Los routers corporativos y los switches multicapa participan en **OSPFv2, proceso 1, área 0**. La WAN usa un triángulo de enlaces seriales. El tramo directo Quito–Guayaquil es el camino de diseño principal y Santo Domingo forma la alternativa de dos tramos. Guayaquil tiene una ruta por defecto hacia el ISP y la anuncia en OSPF. Los enlaces `/30` entre routers y equipos multicapa separan tránsito de las redes de usuario.

Las interfaces VLAN de Distribución en Quito y de los spines en Guayaquil configuran **HSRP**. Los clientes conservan una puerta de enlace virtual terminada en `.1` aunque cambie el equipo que atiende esa dirección. HSRP cubre la redundancia de gateway. La recuperación operativa por sustitución de un switch de acceso depende del cableado de los usuarios y de la configuración disponible para el equipo de reemplazo.

### Agregación y Spanning Tree en Guayaquil

Los switches Spine 1 y Spine 2 agrupan **`Gi1/0/3` y `Gi1/0/4`** en **Port-channel 1**, con LACP activo en ambos extremos. STP ve el port-channel como un enlace lógico. Los cuatro uplinks Leaf–Spine son enlaces individuales. Por eso el canal entre spines añade redundancia y capacidad en ese tramo, mientras STP sigue controlando los bucles de la malla de acceso.

Spine 1 tiene prioridad preferente para las VLAN **10, 30 y 150**, y Spine 2 para la **20**. Esto permite distribuir el papel de switch raíz por VLAN. Las prioridades configuradas describen el diseño. El estado de reenvío por enlace y el rendimiento se comprueban en ejecución.

## Servicios y políticas

| Componente | Configuración y propósito |
| --- | --- |
| **DHCP** | Quito centraliza la asignación en `192.168.100.20` y usa relay en Distribución. Guayaquil define pools en su router. Santo Domingo obtiene su direccionamiento de voz del pool local. |
| **Telefonía IP** | CME y dial peers VoIP en los routers corporativos. Las VLAN 152, 150 y 151 corresponden a las extensiones 1001, 2001 y 3001 de Quito, Guayaquil y Santo Domingo. |
| **TFTP** | `192.168.100.10`, VLAN de servidores de Quito. Centraliza el respaldo de configuraciones. |
| **NO-INTERNET-PROD** | ACL de salida al ISP en Guayaquil. Deniega el acceso externo de `192.168.20.0/24` y contiene permisos previos para destinos corporativos. |
| **PROTECCION-TFTP** | ACL de salida en la interfaz VLAN 100 de los switches de Distribución. Permite las redes de Sistemas de ambas sedes y excepciones explícitas de infraestructura hacia el servidor. |
| **AISLAR-STO** | ACL de entrada del segmento local de Santo Domingo. Permite voz y señalización según los destinos configurados, deniega destinos de datos corporativos y mantiene una excepción de prueba hacia TFTP. |

El escenario de recuperación distingue restaurar la configuración en el mismo equipo de reemplazar un equipo y recuperar su IOS y configuración. La primera operación devuelve VLAN, troncales y parámetros de acceso a un dispositivo funcional. La segunda requiere que el equipo sustituto pueda alcanzar TFTP antes de cargar los respaldos. El archivo de simulación documenta la topología y las configuraciones disponibles. Las llamadas, la convergencia bajo falla y los tiempos de recuperación se evalúan en el simulador.

[Direccionamiento por sede y circuito WAN](direccionamiento.md) · [Topología lógica](../imagenes/topologia-logica.png) · [Mapa físico](../imagenes/mapa-fisico.png)
