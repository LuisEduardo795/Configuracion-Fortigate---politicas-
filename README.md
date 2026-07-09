# Configuración de Seguridad Perimetral con FortiGate
Documentacion de la configuracion de politicas en Fortigate 

1. Objetivo de la Red
El objetivo principal de este proyecto es implementar una infraestructura de red segura utilizando un firewall FortiGate, configurado íntegramente a través de su interfaz gráfica (GUI), que cumpla con los siguientes requisitos:
•	Proporcionar acceso a Internet para los usuarios internos, mediante NAT.
•	Segmentar la red en dos zonas: LAN de Usuarios (/25) y LAN de Servidores (/28).
•	Asignar IP en todas las interfaces del firewall y configurar una ruta por defecto.
•	Habilitar DHCP en la LAN de Usuarios.
•	Permitir únicamente tráfico HTTP desde la LAN de Usuarios hacia la LAN de Servidores, y bloquear todo lo demás.
•	Bloquear el acceso a redes sociales.
•	Bloquear las llamadas de voz/video por WhatsApp.
•	Bloquear dominios y subdominios de itla.edu.do.
•	Detectar y bloquear escáneres de red (ej. Nmap).
•	Proteger el servidor web mediante un Web Application Firewall (WAF).

2. Topología de Red
La topología está compuesta por un firewall FortiGate con tres interfaces: una hacia Internet (WAN), una hacia la LAN de Usuarios y una hacia la LAN de Servidores (DMZ). A continuación se detalla el direccionamiento IP:

2.1 Interfaces y Direccionamiento IP
<img width="621" height="262" alt="image" src="https://github.com/user-attachments/assets/d40f246b-00c2-4477-91f0-1230730789e5" />

2.2 Diagrama de Topología
<img width="561" height="701" alt="image" src="https://github.com/user-attachments/assets/0914b8ac-32c8-4e5d-8f5e-a43ebe420692" />

3. Configuraciones Utilizadas
Todas las configuraciones fueron aplicadas a través de la interfaz gráfica (GUI) del FortiGate. 
A continuación se documenta, con evidencia visual, cada configuración realizada desde la GUI del FortiGate. 

3.1 Configuración de Interfaces

<img width="959" height="382" alt="image" src="https://github.com/user-attachments/assets/2300cf6d-e992-4b58-967c-d6019111b917" />
Se asignaron las direcciones IP estáticas y los roles correspondientes (WAN, LAN, DMZ) a cada interfaz física del firewall.

3.2 Configuración de DHCP - LAN de Usuarios
<img width="554" height="425" alt="image" src="https://github.com/user-attachments/assets/7f930dd4-6d2e-4c0e-ad5c-89a0878fab79" />
El servicio DHCP entrega direcciones desde 10.25.76.10 hasta 10.25.76.120, con gateway 10.25.76.1.

3.3 Políticas de Firewall
<img width="958" height="337" alt="image" src="https://github.com/user-attachments/assets/bd52ba48-cc27-436f-9aaf-a5de4f61f272" />
Se muestran las tres políticas activas en Policy & Objects > Firewall Policy, en el orden correcto de evaluación. Aqui estan las 
reglas de NAT a Internet, permiso HTTP hacia servidores y bloqueo de todo lo demás.

3.4 Web Filter (Redes Sociales y WhatsApp)
<img width="491" height="415" alt="image" src="https://github.com/user-attachments/assets/e79e88db-db69-424d-89f9-1cdda450b1cc" />
Aqui se muestra el Bloqueo de Facebook, Instagram y WhatsApp aplicado en la política de salida a Internet.

3.5 DNS Filter (Bloqueo de ITLA)
<img width="536" height="334" alt="image" src="https://github.com/user-attachments/assets/cdd5217d-62de-4534-a23e-817bbe3a90a7" />
Aqui se ve el Bloqueo de dominios y subdominios de itla.edu.do.

3.6 IPS Sensor (Escáneres de Red)
<img width="461" height="429" alt="image" src="https://github.com/user-attachments/assets/6fee04c2-b201-48df-b311-6618486eb8c6" />
Se filtraron por gravedad y se bloquearon firmas relacionadas con scan, nmap, port scan, FIN Scan, osea herramientas o técnicas de escaneo.

3.7 Perfil WAF
<img width="479" height="466" alt="image" src="https://github.com/user-attachments/assets/299136d3-3678-445a-bd61-21a50fb43fda" />
<img width="493" height="402" alt="image" src="https://github.com/user-attachments/assets/71cfbf46-21cb-4fd0-b07c-4188942945e1" />
Dentro del perfil WAF se habilitaron firmas de seguridad para detectar y bloquear ataques comunes contra aplicaciones web, tales como Cross Site Scripting (XSS), SQL Injection y solicitudes malformadas (Malformed Requests). Estas reglas inspeccionan las peticiones HTTP antes de que lleguen al servidor.

El perfil WAF fue asociado a la política de firewall que permite el tráfico HTTP desde la LAN de usuarios hacia el servidor web. De esta manera, todas las solicitudes HTTP son inspeccionadas por el WAF antes de ser entregadas al servidor, aumentando el nivel de seguridad de la aplicación web

4. Pruebas de Verificación
Se ejecutaron las siguientes pruebas para validar el correcto funcionamiento de cada control de seguridad implementado:

<img width="624" height="241" alt="image" src="https://github.com/user-attachments/assets/a0dfe11e-47ad-40d6-8506-042a8d930928" />

5. Conclusión
Se implementó exitosamente una infraestructura de red segura utilizando un firewall FortiGate, configurada íntegramente a través de su interfaz gráfica. Se cumplieron todos los requisitos establecidos:
•	Acceso a Internet con NAT.
•	Segmentación de red en LAN de Usuarios y LAN de Servidores.
•	Políticas de firewall restrictivas, permitiendo solo HTTP entre segmentos.
•	Controles de seguridad avanzados: Web Filter, DNS Filter, IPS y WAF.
La red está lista para operar en un entorno de producción con altos estándares de seguridad.





