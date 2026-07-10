# 🔒 Configuración de Seguridad Perimetral con FortiGate

Documentación técnica de la implementación de un firewall **FortiGate**, configurado íntegramente desde la **GUI**, con segmentación de red, políticas de firewall, filtrado web/DNS, IPS y WAF.

**Autor:** Luis Eduardo Mañon Mejia
**Matrícula:** 2025-0767
**Fecha:** 9 de julio de 2026

---

## 📑 Tabla de Contenido
- [Objetivo de la Red](#-objetivo-de-la-red)
- [Topología de Red](#-topología-de-red)
- [Configuraciones (GUI)](#-configuraciones-utilizadas-gui)
- [Pruebas de Verificación](#-pruebas-de-verificación)
- [Conclusión](#-conclusión)
- [Video](#-Video)

---

## 🎯 Objetivo de la Red

Implementar una infraestructura de red segura con FortiGate, configurada **100% desde la interfaz gráfica**, que cumpla con:

- Acceso a Internet para los usuarios internos, mediante NAT.
- Segmentación en dos zonas: **LAN de Usuarios (/25)** y **LAN de Servidores (/28)**.
- IP asignada en todas las interfaces del firewall y ruta por defecto configurada.
- DHCP habilitado en la LAN de Usuarios.
- Solo tráfico **HTTP** permitido de Usuarios → Servidores; todo lo demás bloqueado.
- Bloqueo de redes sociales.
- Bloqueo de llamadas de voz/video por WhatsApp.
- Bloqueo de dominios y subdominios de `itla.edu.do`.
- Detección y bloqueo de escáneres de red (Nmap, port scan, FIN scan).
- Protección del servidor web mediante **WAF**.

---

## 🗺️ Topología de Red

La topología está compuesta por un firewall FortiGate con tres interfaces: una hacia Internet (WAN), una hacia la LAN de Usuarios y una hacia la LAN de Servidores (DMZ).

### Direccionamiento IP

| Dispositivo | Interfaz | Red | IP | Rol |
|---|---|---|---|---|
| FortiGate | port1 (WAN) | 10.25.76.0/24 | 10.25.76.100 | Internet |
| FortiGate | port2 (LAN) | 10.25.76.0/25 | 10.25.76.1 | LAN Usuarios |
| FortiGate | port3 (DMZ) | 10.25.76.128/28 | 10.25.76.129 | LAN Servidores |
| Kali (Servidor Web) | eth0 | 10.25.76.128/28 | 10.25.76.130 | Servidor |
| PC1 (VPCS) | eth0 | 10.25.76.0/25 | 10.25.76.10 | Cliente |

<img width="621" height="262" alt="Interfaces y direccionamiento IP" src="https://github.com/user-attachments/assets/d40f246b-00c2-4477-91f0-1230730789e5" />

### Diagrama de Topología

<img width="561" height="701" alt="Diagrama de topología" src="https://github.com/user-attachments/assets/0914b8ac-32c8-4e5d-8f5e-a43ebe420692" />

---

## ⚙️ Configuraciones Utilizadas (GUI)

> Todas las configuraciones se aplicaron desde la interfaz gráfica del FortiGate (sin CLI).

### 1. Interfaces
📍 *Network > Interfaces*

<img width="959" height="382" alt="Configuración de interfaces" src="https://github.com/user-attachments/assets/2300cf6d-e992-4b58-967c-d6019111b917" />

Se asignaron las direcciones IP estáticas y los roles correspondientes (WAN, LAN, DMZ) a cada interfaz física del firewall.

### 2. DHCP – LAN de Usuarios
📍 *Network > Interfaces > port2 > DHCP Server*

<img width="554" height="425" alt="Servidor DHCP" src="https://github.com/user-attachments/assets/7f930dd4-6d2e-4c0e-ad5c-89a0878fab79" />

El servicio DHCP entrega direcciones desde `10.25.76.10` hasta `10.25.76.120`, con gateway `10.25.76.1`.

### 3. Políticas de Firewall
📍 *Policy & Objects > Firewall Policy*

<img width="958" height="337" alt="Políticas de firewall" src="https://github.com/user-attachments/assets/bd52ba48-cc27-436f-9aaf-a5de4f61f272" />

Las tres políticas activas, en el orden correcto de evaluación:
1. **NAT a Internet** — LAN Usuarios → WAN, todo el tráfico, con NAT habilitado.
2. **Allow HTTP to Servers** — LAN Usuarios → LAN Servidores, solo servicio HTTP.
3. **Deny All to Servers** — LAN Usuarios → LAN Servidores, bloquea el resto.

### 4. Web Filter — Redes Sociales y WhatsApp
📍 *Security Profiles > Web Filter*

<img width="491" height="415" alt="Perfil Web Filter" src="https://github.com/user-attachments/assets/e79e88db-db69-424d-89f9-1cdda450b1cc" />

Bloqueo de Facebook, Instagram y WhatsApp, aplicado en la política de salida a Internet.

### 5. DNS Filter — Bloqueo de ITLA
📍 *Security Profiles > DNS Filter*

<img width="536" height="334" alt="Perfil DNS Filter" src="https://github.com/user-attachments/assets/cdd5217d-62de-4534-a23e-817bbe3a90a7" />

Bloqueo de dominios y subdominios de `itla.edu.do`.

### 6. IPS Sensor — Escáneres de Red
📍 *Security Profiles > Intrusion Prevention*

<img width="461" height="429" alt="Perfil IPS" src="https://github.com/user-attachments/assets/6fee04c2-b201-48df-b311-6618486eb8c6" />

Se filtraron y bloquearon firmas relacionadas con `scan`, `nmap`, `port scan` y `FIN scan` — herramientas y técnicas comunes de escaneo de red.

### 7. Perfil WAF — Protección del Servidor Web
📍 *Security Profiles > Web Application Firewall*

<img width="479" height="466" alt="Perfil WAF - firmas" src="https://github.com/user-attachments/assets/299136d3-3678-445a-bd61-21a50fb43fda" />
<img width="493" height="402" alt="Perfil WAF - constraints" src="https://github.com/user-attachments/assets/71cfbf46-21cb-4fd0-b07c-4188942945e1" />

Se habilitaron firmas para detectar y bloquear ataques comunes contra aplicaciones web: **Cross Site Scripting (XSS)**, **SQL Injection** y solicitudes malformadas (*Malformed Requests*). Estas reglas inspeccionan las peticiones HTTP antes de que lleguen al servidor.

El perfil WAF se asoció a la política de firewall que permite tráfico HTTP desde la LAN de Usuarios hacia el servidor web, de modo que toda solicitud HTTP es inspeccionada antes de llegar al servidor.

---

## 🧪 Pruebas de Verificación

<img width="624" height="241" alt="Pruebas de verificación" src="https://github.com/user-attachments/assets/a0dfe11e-47ad-40d6-8506-042a8d930928" />

| Prueba | Resultado esperado |
|---|---|
| Acceso a Internet (`ping 8.8.8.8`) | ✅ Éxito |
| Acceso HTTP al servidor | ✅ Página mostrada |
| WAF — ataque XSS | ❌ Bloqueado |
| Acceso a redes sociales | ❌ Bloqueado |
| Acceso a WhatsApp | ❌ Bloqueado |
| Acceso a itla.edu.do | ❌ Bloqueado |
| Escáner de red (Nmap) | ❌ Bloqueado |

---

## ✅ Conclusión

Se implementó exitosamente una infraestructura de red segura utilizando un firewall FortiGate, configurada íntegramente a través de su interfaz gráfica. Se cumplieron todos los requisitos establecidos:

- Acceso a Internet con NAT.
- Segmentación de red en LAN de Usuarios y LAN de Servidores.
- Políticas de firewall restrictivas, permitiendo solo HTTP entre segmentos.
- Controles de seguridad avanzados: Web Filter, DNS Filter, IPS y WAF.

La red está lista para operar en un entorno de producción con altos estándares de seguridad. 🚀

---
Video: https://youtu.be/k_HiktJOxjs?si=3Z8C51lmSgloFXro

