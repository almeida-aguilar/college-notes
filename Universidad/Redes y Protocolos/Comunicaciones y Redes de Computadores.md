# Capítulo 1: Introducción a las comunicaciones de datos y redes
## 1.1. Un modelo para las comunicaciones
![[2026-04-05T12 33 45,959775954-05 00.png]]

- La **fuente** genera la información.
- El **transmisor** transforma la información de la fuente (bits) a una señal analógica.
- El **sistema de transmisión** es el medio donde la señal analógica transmitia se transporta.
- El **receptor** toma la señal transmitida e invierte la transformación pasando la señal analógica a bits.
- El **destino** tomas los datos del receptor.
---

## 1.2. Comunicaciones de datos
![[2026-04-05T13 20 27,718081388-05 00.png]]

<center>string -> bit -> analog* -> bit*-> string*</center>
<center>*: un error pudo pasar</center>
<center>el error se propaga</center>

> [!NOTE]
> Hay casos donde la perdida de información no suficientemente significativa como para hacer correcciones, mayormente en contextos donde dicha información pueda ser rellenada o corregida a traves del ingenio del mismo usuario.

---
## 1.3. Redes de transmisión de datos
### Redes de área amplia (WAN)

> [!NOTA]
> El pequeño tamaño de los paquetes puede estar relacionada con el manejo de la RAM. Se sabe que la RAM tiene diferentes capas de acceso, algunas más rápidas que otras, siendo las más rápidas también las más pequeñas. Dado esto, es posible que el tamaño de los paquetes esté hecho para ser almacenado en una pequeña ventana de tiempo en un nodo y al instante poder acceder a este mismo para mandarlo al próximo nodo.

#### ATM
En simples palabras, **ATM** es un remplazo directo de la **conmutación de circuitos y de paquetes** y de la **retransmisión de tramas**. Este le da un tamaño fijo a los paquetes, y hace que que los canales y sus velocidades sean dinámicas. Además, dado a la evolución de la misma tecnológica del los dispositivos de **origen** y **destino** a estas se les puede delegar la corrección y detección de errores o huecos en la información.

### Redes de área local (LAN)
- No hay manera objetiva de constatar si una red es **WAN** o **LAN**.
- Mayormente las redes **LAN** son las que usan recursos locales para su conexión o recursos externos pero con conexiones directas.
- Por lo general, las velocidades entre dispositivos en una red **LAN** es mayor a las de una red **WAN**.
## 1.4. Un ejemplo de configuración
![[2026-04-05T15 56 28,564341188-05 00.png]]
# Capítulo 2: Arquitectura de protocolos
## 2.1. ¿Por qué es necesaria una arquitectura de protocolos?
Además de hacer un camino entre 2 dispositivos se deben realizar otras operaciones adicionales:
1. Debe o activar un camino directo o dirigir a la red a sistema destino deseado.
2. La fuente debe preparar al destino para recibir dichos datos.
3. El software debe que transfiere los datos debe asegurarse que el software de destino acepte y almacene los datos.
4. Si el formato de los datos enviados es incompatible con el entorno de destino, entonces, se debe realizar una traducción.
**Protocolo:**
- Sintaxis: formato de los bloques de datos.
- Semántica: coordinación y gestión de errores.
- Temporización: sintonización de velocidades y secuenciación.
## 2.2. Una arquitectura de protocolos simple
## 2.3. OSI
## 2.4. Arquitectura de protocolos TCP/IP
## 2.5. Lecturas recomendadas y sitios web
## 2.6. Términos clave, cuestiones de repaso y ejercicios
