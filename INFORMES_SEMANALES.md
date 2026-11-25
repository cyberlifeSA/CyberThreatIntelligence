# Resumen CTI Semanal — Semana del 16 al 22 de noviembre de 2026

**Semana cubierta:** lunes 16/11/2026 al domingo 22/11/2026
**Generado:** lunes 23/11/2026 09:10 (America/Santiago)
**Fuentes:** INFORMES_DIARIOS.md (18/11, 20/11, 21/11, 22/11), CISA KEV, NIST NVD, MITRE ATT&CK, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) · 🟠 Alta (7-8.9) · 🟡 Media (4-6.9) · 🟢 Baja (<4).

---

> ⚡ **Resumen en 30 segundos:** CISA añadió 9 CVEs al KEV del 17 al 20/11 (Puppet Enterprise, Synology DSM, LiteLLM, Apache Axis, REDCap, VMware vCenter, Apache Roller, Cisco Expressway, SAP NetWeaver). NGINX con PoC público (CVE-2026-123007, 9.2) y SonicWall/SonicOS con bypass SAML MFA siguen activos. Prioridad: cerrar deadlines 23/11 (vCenter, Roller, Expressway, Synology, SAP) y 24/11 (Outlook NTLMv2).

**Leyenda de severidad:** 🔴 Crítica · 🟠 Alta · 🟡 Media · 🟢 Baja.

---

## 1. Turno de vigilancia — qué atender primero

### Top 5 atacar hoy

| # | CVE | Severidad | Acción | Tiempo est. |
|---|---|---|---|---|
| 1 | CVE-2026-59310 / CVE-2026-123890 (VMware vCenter) | 🔴 9.8/10.0 | Parchear vCenter. Deadline 23/11. Ransomware activo. | 1-2 h |
| 2 | CVE-2026-129448 (Apache Roller) | 🔴 9.8 | Actualizar a 6.1.6 o deshabilitar XML-RPC. Deadline 23/11. | 30 min-1 h |
| 3 | CVE-2026-76466 (Cisco Expressway) | 🔴 9.8 | Actualizar a X15.5.20. Creds hard-coded. Deadline 23/11. | 1-2 h |
| 4 | CVE-2024-10441 (Synology DSM) | 🔴 9.8 | Parchear DSM. Ransomware C3RB3R activo. Deadline 23/11. | 1-2 h |
| 5 | CVE-2026-123007 (NGINX) | 🔴 9.2 | Actualizar a 1.31.9/1.30.7 o deshabilitar HTTP/2. PoC público. | 30 min-1 h |

### Vista rápida (CVE → severidad → estado → acción)

| CVE | Severidad | Estado | Acción |
|---|---|---|---|
| CVE-2026-59310 (vCenter) | 🔴 9.8 | KEV, deadline 23/11 | Parchear vCenter |
| CVE-2026-123890 (vCenter auth bypass) | 🔴 10.0 | KEV, deadline 23/11 | Parchear vCenter (VMSA-2026-0006.1) |
| CVE-2026-129448 (Roller) | 🔴 9.8 | KEV, deadline 23/11 | Actualizar a 6.1.6 |
| CVE-2026-76466 (Expressway) | 🔴 9.8 | KEV, deadline 23/11 | Actualizar a X15.5.20 |
| CVE-2024-10441 (Synology DSM) | 🔴 9.8 | KEV, deadline 23/11 | Parchear DSM |
| CVE-2025-42884 (SAP NetWeaver EP) | 🟡 6.5 | KEV, deadline 23/11 | SAP Note 3660969 |
| CVE-2026-124194 (Outlook) | 🟡 6.5 | KEV, deadline 24/11 | Updates noviembre Office |
| CVE-2026-123007 (NGINX) | 🔴 9.2 | PoC público | Actualizar a 1.31.9/1.30.7 |
| CVE-2026-19868 (SonicWall) | 🟠 — | KEV desde 06/11 | Actualizar SonicOS |
| CVE-2026-115731 (Puppet Enterprise) | 🟠 8.8 | KEV, deadline 01/12 | Actualizar Puppet Enterprise |
| CVE-2026-90817 (REDCap) | 🔴 ~9.8 | KEV, deadline 21/11 (vencido) | Actualizar REDCap |
| CVE-2026-90436 (Apache Axis 1.x) | 🔴 Crítico | KEV, deadline 21/11 (vencido) | Eliminar Axis del classpath |
| CVE-2026-40217 (LiteLLM) | 🟠 8.8 | KEV, deadline 02/12 | Actualizar LiteLLM |
| CVE-2026-114938 (Java4Shell) | 🔴 Crítico | Alerta CISA, no KEV | Parchear JDK |

### Prioridades

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 - vCenter (CVE-2026-59310 + CVE-2026-123890) | Parchear vCenter a 9.1.1.0200/9.0.2.0200/8.0 U3m/5.2.5 (VMSA-2026-0006.1). Directory traversal en Syslog + auth bypass en VMware Directory Service. Ransomware activo por actor China-nexus. Deadline federal 23/11. |
| 2 | 🔴 P2 - Apache Roller (CVE-2026-129448) | Actualizar a 6.1.6 o deshabilitar XML-RPC. Deserialización pre-auth en XML-RPC. Explotación activa. Deadline federal 23/11. |
| 3 | 🔴 P3 - Cisco Expressway (CVE-2026-76466) | Actualizar a X15.5.20. Credenciales hard-coded en database service de clustered devices. RCE como root. Deadline federal 23/11. |
| 4 | 🔴 P4 - Synology DSM (CVE-2024-10441) | Parchear DSM. Ransomware C3RB3R activo. Deadline federal 23/11. |
| 5 | 🟡 P5 - SAP NetWeaver EP (CVE-2025-42884) | Aplicar SAP Note 3660969. SQL/JNDI injection en portal. Deadline federal 23/11. |
| 6 | 🟠 P6 - Outlook (CVE-2026-124194) | Aplicar updates de Office noviembre 2026. Bloquear SMB/NTLM saliente. Deadline federal 24/11. |
| 7 | 🟡 General | Vigilar NVD/KEV; verificar Shadowserver para explotación en Internet; cerrar CVEs con deadline vencido (REDCap, Axis). |

> ℹ️ CISA añadió 9 CVEs al KEV del 17 al 20/11. NGINX tiene PoC público pero no está en KEV aún. Java4Shell (CVE-2026-114938) es alerta de CISA pero no está en KEV.

---

## 2. Recomendaciones SOC L1

1. 🔴 **Prioridad 1 - VMware vCenter (CVE-2026-59310 + CVE-2026-123890):** inventariar instancias vCenter; parchear a 9.1.1.0200/9.0.2.0200/8.0 U3m/5.2.5. Revisar logs de Syslog service y VMware Directory Service. Buscar actividad de ransomware (archivos cifrados, procesos inusuales en ESXi).
2. 🔴 **Prioridad 2 - Apache Roller (CVE-2026-129448):** inventariar instancias Roller 4.0.0-6.1.5; actualizar a 6.1.6 o deshabilitar XML-RPC. Revisar logs de /xmlrpc para requests con vendor extension types.
3. 🔴 **Prioridad 3 - Cisco Expressway (CVE-2026-76466):** inventariar clustered Expressway-C/E; actualizar a X15.5.20. Revisar logs de database service para RPC commands anómalos.
4. 🔴 **Prioridad 4 - Synology DSM (CVE-2024-10441):** inventariar NAS Synology; parchear DSM. Buscar indicadores de C3RB3R (archivos .c3rb3r, ransom note).
5. 🟡 **Prioridad 5 - SAP NetWeaver EP (CVE-2025-42884):** inventariar SAP NetWeaver Enterprise Portal; aplicar SAP Note 3660969. Revisar logs de portal para SQL/JNDI injection.
6. 🟠 **Prioridad 6 - Microsoft Outlook (CVE-2026-124194):** aplicar updates de Office noviembre 2026. Alertar a usuarios no abrir archivos de mensaje inesperados. Bloquear SMB/NTLM saliente.
7. 🟡 **Prioridad 7 - NGINX (CVE-2026-123007):** inventariar servidores NGINX con HTTP/2; parchear a 1.31.9/1.30.7 o deshabilitar HTTP/2. Verificar que no haya proxies HTTP/2 terminando delante.
8. 🟡 **General:** verificar CVEs con deadline vencido (REDCap 21/11, Axis 21/11); asumir compromiso y cazar IOCs si los hay.

---

## 3. Recomendaciones SOC L2

1. 🔴 **Hardening (vCenter):** restringir acceso de red al Syslog service y VMware Directory Service; segmentar vCenter de la red de producción; verificar integridad de backups de VMs.
2. 🔴 **Hardening (Roller/Axis):** deshabilitar XML-RPC en Roller si no se usa; eliminar Axis 1.x del classpath; implementar WAF con firmas para deserialización Java.
3. 🟠 **Hardening (Expressway/SonicWall):** restringir acceso de red al database service de Expressway; actualizar SonicOS; forzar MFA en todos los métodos de auth SSLVPN.
4. 🟠 **Threat hunting:** buscar en vCenter actividad de ransomware (archivos cifrados, procesos inusuales en ESXi); buscar webshells en NGINX; buscar RPC anómalos en Expressway; buscar actividad C3RB3R en Synology.
5. 🟠 **Tuning de detecciones:** crear reglas Sigma para deserialización Java en XML-RPC (Roller); alertar sobre accesos al Syslog service de vCenter desde IPs no reconocidas; detectar RPC commands anómalos en Expressway; alertar ante nuevas adiciones KEV.
6. 🟡 **Automatización:** integrar KEV en pipeline de priorización; alertar automáticamente ante nuevas adiciones con deadline federal; monitorear NVD para CVEs con PoC público.
7. 🟡 **Mejoras de proceso:** documentar estado de remediación por CVE; usar fechas KEV (BOD 26-04) como SLA interno; mantener inventario actualizado de software expuesto a Internet.

---

## 4. Resumen ejecutivo

- **9 nuevos CVEs en KEV del 17 al 20/11:** Puppet Enterprise (CVE-2026-115731, 17/11), Synology DSM (CVE-2024-10441), LiteLLM (CVE-2026-40217), Apache Axis (CVE-2026-90436), REDCap (CVE-2026-90817) (18/11), VMware vCenter (CVE-2026-59310 + CVE-2026-123890), Apache Roller (CVE-2026-129448), Cisco Expressway (CVE-2026-76466), SAP NetWeaver EP (CVE-2025-42884) (20/11). Deadlines 21/11, 23/11, 01/12, 02/12.
- **vCenter bajo ataque activo:** actor China-nexus desplegando ransomware mediante CVE-2026-59310 (directory traversal en Syslog) + CVE-2026-123890 (auth bypass en VMware Directory Service). Parche disponible desde agosto 2026.
- **Apache Roller con RCE pre-auth:** deserialización de vendor extension types en XML-RPC antes de autenticación. Afecta 4.0.0-6.1.4. Fix en 6.1.6.
- **Cisco Expressway RCE como root:** credenciales hard-coded en database service de clustered devices. Sin workaround; solo parche X15.5.20.
- **NGINX crítico con PoC público:** CVE-2026-123007 (CVSS 9.2) y 4 CVEs hermanos afectan NGINX 1.9.5-1.31.8 con HTTP/2. PoC público en GitHub; explotación confirmada en cloud. Parche disponible (1.31.9/1.30.7).
- **SonicWall SonicOS sigue en KEV:** CVE-2026-19868 (bypass SAML MFA en SSLVPN). Explotación activa desde 06/11. Actualizar a 7.3.4-7013/8.2.3-8015.
- **Java4Shell (CVE-2026-114938):** alerta de CISA del 19/11. Memory corruption en Base64 decoder del HotSpot JVM. No está en KEV. Parchear JDKs y runtimes Java, incluyendo los embebidos en apps y contenedores.

---

## 5. Vulnerabilidades críticas de la semana

### 5.1 CVE-2026-123890 — VMware vCenter Server (Authentication Bypass en VMware Directory Service)

- **En simple:** Una vulnerabilidad en VMware vCenter Server permite a un atacante no autenticado omitir la autenticación del VMware Directory Service (vmdir) mediante un path alternativo. Combinada con CVE-2026-59310 (directory traversal en Syslog), permite RCE completo. CISA la añadió al KEV el 20/11.
- **CVE:** CVE-2026-123890 · **CVSS:** 10.0 (Crítica) · **CWE:** CWE-287 (improper authentication) · **EPSS:** no disponible · **Score combinado CVSS×EPSS:** N/A
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1078 (Valid Accounts)
- **Productos afectados:** VMware vCenter Server 9.1.x, 9.0.x, 8.0, 5.x; Telco Cloud Platform 3.0, 4.x, 5.0.x, 5.1.x, 5.2; Telco Cloud Infrastructure 3.0. Fix en VMSA-2026-0006.1.
- **Explotación:** activa (KEV el 20/11). Atacante China-nexus desplegando ransomware.
- **Mitigación:** actualizar vCenter a la versión fija de la línea correspondiente (9.1.1.0200, 9.0.2.0200, 8.0 U3m, 5.2.5). Revisar logs de VMware Directory Service.
- **IOCs:** actividad anómala en vmdir; accesos no autorizados a vCenter; archivos cifrados en VMs/ESXi.
- **Sigma:** `title: VMware vCenter Auth Bypass (CVE-2026-123890) | description: Detects unauthenticated access attempts to VMware Directory Service | logsource: application | detection: selection: event_type: 'vmdir_auth' AND auth_status: 'bypassed' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión vCenter; revisar logs de vmdir; buscar accesos no autorizados.
  - **Contención:** actualizar vCenter; restringir acceso de red a vmdir; segmentar vCenter.
  - **Verificación:** confirmar versión parcheada; validar ausencia de accesos anómalos post-parche.
- **Fuentes:** [CISA KEV 20/11](https://www.cisa.gov/news-events/alerts/2026/11/20/cisa-adds-four-known-exploited-vulnerabilities-catalog) · [NVD CVE-2026-123890](https://nvd.nist.gov/vuln/detail/CVE-2026-123890)

### 5.2 CVE-2026-59310 — VMware vCenter Server (Directory Traversal en Syslog)

- **En simple:** Una vulnerabilidad de directory traversal en el servicio Syslog de vCenter permite a un atacante remoto ejecutar código arbitrario. Actor China-nexus la usa para desplegar ransomware. CISA la añadió al KEV el 20/11 con deadline 23/11.
- **CVE:** CVE-2026-59310 · **CVSS:** 9.8 (Crítica) · **CWE:** CWE-22 (improper limitation of a pathname to a restricted directory) · **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1486 (Data Encrypted for Impact)
- **Productos afectados:** VMware vCenter Server (versiones no parcheadas). Fix en 9.1.1.0200, 9.0.2.0200, 8.0 U3m, 5.2.5 (Broadcom VMSA-2026-0006.1).
- **Explotación:** activa (KEV el 20/11). Actor China-nexus desplegando ransomware. Parche disponible desde agosto 2026.
- **Mitigación:** actualizar vCenter a la versión fija. Revisar logs de Syslog service y buscar actividad de ransomware.
- **IOCs:** archivos cifrados en VMs/ESXi hosts; procesos inusuales en ESXi; actividad anómala en Syslog service.
- **Sigma:** `title: VMware vCenter Syslog Directory Traversal (CVE-2026-59310) | description: Detects anomalous file access patterns in vCenter Syslog service | logsource: file_event | detection: selection: TargetFilename|contains: '../' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión vCenter; revisar logs de Syslog service; buscar archivos cifrados en ESXi.
  - **Contención:** aislar vCenter de la red si se sospecha compromiso; deshabilitar acceso remoto innecesario; verificar integridad de backups.
  - **Verificación:** confirmar versión parcheada; validar ausencia de actividad anómala post-parche.

### 5.3 CVE-2026-129448 — Apache Roller (Deserialización XML-RPC)

- **En simple:** Una vulnerabilidad de deserialización de datos no confiables en el endpoint XML-RPC de Apache Roller permite RCE sin autenticación. Afecta versiones 4.0.0-6.1.4. CISA la añadió al KEV el 20/11.
- **CVE:** CVE-2026-129448 · **CVSS:** 9.8 (Crítica) · **CWE:** CWE-502 (deserialización de datos no confiables) · **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1059 (Command and Scripting Interpreter)
- **Productos afectados:** Apache Roller 4.0.0-6.1.4 (también CVE-2026-82384 para 6.1.5, mismo bug). Fix en 6.1.6.
- **Explotación:** activa (KEV el 20/11). Explotación confirmada en la calle.
- **Mitigación:** actualizar a 6.1.6 o deshabilitar XML-RPC. Implementar WAF con firmas para deserialización Java.
- **IOCs:** requests HTTP POST a /xmlrpc con vendor extension types; procesos hijo inesperados de la JVM.
- **Sigma:** `title: Apache Roller XML-RPC Deserialization (CVE-2026-129448) | description: Detects HTTP POST requests to Apache Roller XML-RPC endpoint with suspicious vendor extension types | logsource: web | detection: selection: cs-method: 'POST' AND cs-uri-stem|contains: '/xmlrpc' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión Roller; revisar logs de /xmlrpc; buscar procesos hijo inesperados de la JVM.
  - **Contención:** actualizar a 6.1.6 o deshabilitar XML-RPC; implementar WAF; segmentar el servidor.
  - **Verificación:** confirmar versión parcheada; validar ausencia de actividad anómala.

### 5.4 CVE-2026-76466 — Cisco Expressway Series (Credenciales hard-coded)

- **En simple:** Credenciales hard-coded en el database service de dispositivos Cisco Expressway-C/E clustered permiten RCE como root sin autenticación mediante RPC commands. CISA la añadió al KEV el 20/11.
- **CVE:** CVE-2026-76466 · **CVSS:** 9.8 (Crítica) · **CWE:** CWE-798 (use of hard-coded credentials) · **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1078 (Valid Accounts)
- **Productos afectados:** Cisco Expressway-C (Core) y Expressway-E (Edge) clustered devices. Fix en X15.5.20.
- **Explotación:** activa (KEV el 20/11). Explotación confirmada (SecurityWeek, SecurityAffairs).
- **Mitigación:** actualizar a X15.5.20. No hay workaround. Revisar logs de database service.
- **IOCs:** RPC commands anómalos al database service; accesos no autorizados a Expressway-C/E.
- **Sigma:** `title: Cisco Expressway DB Service RCE (CVE-2026-76466) | description: Detects anomalous RPC commands to Expressway database service | logsource: network | detection: selection: destination_port: 0 AND protocol: 'RPC' | condition: selection`
- **Playbook:**
  - **Triage:** inventariar clustered Expressway-C/E; verificar versión; revisar logs de database service.
  - **Contención:** actualizar a X15.5.20; restringir acceso de red; segmentar dispositivos.
  - **Verificación:** confirmar versión parcheada; validar ausencia de RPC commands anómalos.

### 5.5 CVE-2025-42884 — SAP NetWeaver Enterprise Portal (SQL/JNDI Injection)

- **En simple:** Una vulnerabilidad de inyección SQL/JNDI en SAP NetWeaver Enterprise Portal permite a un atacante no autenticado divulgar o modificar datos del servidor. CISA la añadió al KEV el 20/11.
- **CVE:** CVE-2025-42884 · **CVSS:** 6.5 (Media) · **CWE:** CWE-89 (improper neutralization of special elements used in an SQL command) · **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application)
- **Productos afectados:** SAP NetWeaver Enterprise Portal. Fix en SAP Note 3660969.
- **Explotación:** activa (KEV el 20/11).
- **Mitigación:** aplicar SAP Note 3660969. Revisar logs de portal.
- **IOCs:** requests HTTP con parámetros SQL/JNDI sospechosos en el portal; errores SQL en logs.
- **Sigma:** `title: SAP NetWeaver Portal SQL Injection (CVE-2025-42884) | description: Detects HTTP requests to SAP NetWeaver Enterprise Portal with suspicious SQL parameters | logsource: web | detection: selection: cs-uri-stem|contains: 'portal' AND cs-uri-query|contains: 'SELECT' OR cs-uri-query|contains: 'UNION' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión SAP NetWeaver EP; revisar logs de portal; buscar errores SQL anómalos.
  - **Contención:** aplicar SAP Note 3660969; restringir acceso al portal; segmentar el servidor.
  - **Verificación:** confirmar nota SAP aplicada; validar ausencia de SQL injection post-parche.

### 5.6 CVE-2026-124194 — Microsoft Outlook (Spoofing / NTLMv2 Leak)

- **En simple:** Una falla en Microsoft Outlook no avisa al usuario antes de acciones inseguras. Un atacante envía un archivo de mensaje manipulado que, al abrirse, hace que Outlook se conecte a un servidor controlado por el atacante sin mostrar la advertencia habitual, filtrando el hash NTLMv2 del usuario. CISA lo incluyó en KEV con deadline 24/11.
- **CVE:** CVE-2026-124194 · **CVSS:** 6.5 (Media) · **CWE:** CWE-356 · **EPSS:** 1.9% (30 días)
- **MITRE ATT&CK:** T1557 (Adversary-in-the-Middle) / T1187 (Forced Authentication)
- **Productos afectados:** Microsoft 365 Apps for Enterprise; Office LTSC 2021 (< build 16.0.14334.21048); Office LTSC 2024 (< build 16.0.17932.21140)
- **Explotación:** KEV (añadido 10/11/2026), explotado en la calle. Código de exploit maduro.
- **Mitigación:** instalar updates de noviembre 2026 de Office. Bloquear SMB y NTLM saliente a Internet.
- **IOCs:** No publicados por Microsoft.
- **Sigma:** Detección de conexiones SMB/NTLM salientes inusuales desde procesos de Outlook.
- **Playbook:**
  - **Triage:** verificar versión de Office; confirmar si el usuario abrió archivos de mensaje inesperados; buscar conexiones SMB salientes.
  - **Contención:** aplicar parche; bloquear SMB/NTLM saliente; aislar endpoints sospechosos.
  - **Verificación:** confirmar build de Office; revisar logs de firewall/proxy.

### 5.7 CVE-2026-123007 y hermanos — NGINX (HTTP/2 Memory Corruption / RCE)

- **En simple:** Cinco CVEs críticos afectan NGINX 1.9.5-1.31.8 con HTTP/2 habilitado. PoC público en GitHub con RCE completo. Explotación confirmada en cloud (AWS, GCP, Azure). Requiere ~128 GB de tráfico HTTP/2 y derrota de ASLR.
- **CVEs:** CVE-2026-123007 (heap buffer overflow, HTTP/2), CVE-2026-123633 (heap buffer overflow, HTTP/2), CVE-2026-123634 (use-after-free, HTTP/2), CVE-2026-121810 (integer overflow, HTTP/2), CVE-2026-113425 (use-after-free, mail proxy)
- **CVSS:** 9.2 (Crítico, CVSS 4.0) / 8.1 (Alta, CVSS 3.1) · **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1499 (Endpoint Denial of Service)
- **Productos afectados:** NGINX 1.9.5-1.30.6 y 1.31.0-1.31.8 (HTTP/2); mail proxy 0.5.33-1.30.6 y 1.31.0-1.31.8. NGINX Plus R34-R36. NGINX Gateway Fabric 2.0.0-2.8.1.
- **Explotación:** PoC público en GitHub (sfewer-r7/CVE-2026-123007). Explotación confirmada en cloud. No está en KEV aún.
- **Mitigación:** actualizar a 1.31.9 (mainline) o 1.30.7 (stable). Mitigación temporal: deshabilitar HTTP/2.
- **IOCs:** tráfico HTTP/2 con volumen anómalo de control frames (WINDOW_UPDATE, RST_STREAM); conexiones HTTP/2 de larga duración con alto volumen de datos.
- **Sigma:** `title: NGINX HTTP/2 Anomalous Traffic (CVE-2026-123007) | description: Detects high volume of HTTP/2 control frames | logsource: network | detection: selection: protocol: 'HTTP/2' AND frame_type: 'WINDOW_UPDATE' OR frame_type: 'RST_STREAM' | condition: selection | count: >1000 per minute`
- **Playbook:**
  - **Triage:** inventariar servidores NGINX con HTTP/2; verificar versión; revisar logs de acceso HTTP/2.
  - **Contención:** parchear a 1.31.9/1.30.7; deshabilitar HTTP/2 como mitigación temporal; implementar IPS.
  - **Verificación:** confirmar versión parcheada; validar que HTTP/2 esté deshabilitado si no se parcheó.

### 5.8 CVE-2024-10441 — Synology DiskStation Manager (DSM) (Ransomware C3RB3R)

- **En simple:** Una vulnerabilidad de improper encoding en el system plugin daemon de Synology DSM permite RCE como root. Ransomware C3RB3R la está explotando activamente contra NAS sin parchear. CISA la añadió al KEV el 18/11.
- **CVE:** CVE-2024-10441 · **CVSS:** 9.8 (Crítica) · **CWE:** CWE-116 (improper encoding or escaping of output) · **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1486 (Data Encrypted for Impact)
- **Productos afectados:** Synology DSM < 7.2.2-72806-1, < 7.2.1-69057-6, < 7.2-64570-4, < 7.1.1-42962-7, < 6.2.4-25556-8. BeeStation OS (BSM) < 1.1-65374.
- **Explotación:** activa (KEV el 18/11). C3RB3R ransomware desplegándose contra NAS sin parchear. Parche disponible desde dic 2024.
- **Mitigación:** actualizar DSM a la versión fija; si no es posible, aislar del Internet. Verificar integridad de backups offline.
- **IOCs:** archivos con extensión .c3rb3r; ransom note típica de C3RB3R; procesos inusuales en NAS.
- **Sigma:** `title: Synology DSM C3RB3R Ransomware Activity | description: Detects file creation with .c3rb3r extension on Synology DSM | logsource: file_event | detection: selection: TargetFilename|endswith: '.c3rb3r' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión DSM; buscar archivos .c3rb3r o ransom notes; revisar logs de acceso al NAS.
  - **Contención:** aislar NAS de la red; deshabilitar acceso remoto; verificar integridad de backups.
  - **Verificación:** confirmar versión DSM parcheada; validar ausencia de archivos cifrados.

### 5.9 CVE-2026-90817 — Vanderbilt University REDCap (Code Injection / RCE)

- **En simple:** Una vulnerabilidad de code injection en REDCap permite RCE sin autenticación mediante manipulación de parámetros de file-path/stream en el routing de survey passthrough. Requiere conocimiento de un hash de encuesta pública válido. CISA la añadió al KEV el 18/11 con deadline 21/11.
- **CVE:** CVE-2026-90817 · **CVSS:** ~9.8 (Crítica) · **CWE:** CWE-73 (external control of file name or path) · **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1059 (Command and Scripting Interpreter)
- **Productos afectados:** REDCap 13.3.0 y superior. Corregido en 16.0.49, 17.3.10 y 17.4.4.
- **Explotación:** activa (KEV el 18/11). Deadline federal 21/11 (vencido).
- **Mitigación:** actualizar a 16.0.49 / 17.3.10 / 17.4.4. Revisar logs de acceso a endpoints __passthru y Data Import.
- **IOCs:** requests HTTP anómalos a endpoints __passthru con parámetros de file-path o stream manipulados; procesos hijo inesperados del servidor web.
- **Sigma:** `title: REDCap Survey Passthrough RCE Attempt | description: Detects HTTP requests to REDCap __passthru endpoint with suspicious file-path parameters | logsource: web | detection: selection: cs-uri-stem|contains: '__passthru' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión REDCap; revisar logs de __passthru y Data Import; buscar procesos hijo inesperados.
  - **Contención:** actualizar REDCap; restringir acceso a encuestas públicas si no son necesarias; segmentar el servidor.
  - **Verificación:** confirmar versión parcheada; validar ausencia de actividad anómala.

### 5.10 CVE-2026-90436 — Apache Axis 1.x (Unsafe Reflection / RCE)

- **En simple:** Una vulnerabilidad de unsafe reflection en Apache Axis 1.x (EOL) permite RCE sin autenticación mediante SOAP Fault handling. El atacante envía un SOAP Fault con `<exceptionName>` que nombra una clase arbitraria. Rapid7 detectó explotación contra Confluence Data Center y N-central el 06/11. No hay parche (EOL); la única mitigación es eliminar Axis del classpath. CISA la añadió al KEV el 18/11 con deadline 21/11.
- **CVE:** CVE-2026-90436 · **CVSS:** Crítico (Snyk reporta 10.0) · **CWE:** CWE-470 (use of externally-controlled input to select classes or code) · **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) — MITRE no documenta técnica específica para unsafe reflection; T1190 es la más cercana.
- **Productos afectados:** Apache Axis 1.x (todas las versiones hasta 1.4, EOL). Afecta apps que usan Axis 1.x: Confluence Data Center, N-able N-central.
- **Explotación:** activa (KEV el 18/11). Rapid7 detectó explotación el 06/11. Huntress reportó 2 intentos el 12/11. Deadline federal 21/11 (vencido).
- **Mitigación:** no hay parche. Eliminar Axis 1.x del classpath o actualizar la app host. N-able deshabilitó la feature "Legacy API".
- **IOCs:** requests SOAP con `<exceptionName>` sospechoso en el body; procesos hijo inesperados de la JVM de Confluence/N-central.
- **Sigma:** `title: Apache Axis Unsafe Reflection Exploit Attempt | description: Detects SOAP requests containing exceptionName element targeting Axis 1.x | logsource: web | detection: selection: cs-uri-stem|contains: 'axis' | condition: selection`
- **Playbook:**
  - **Triage:** buscar JARs de org.apache.axis:axis en classpaths; revisar logs de acceso SOAP; buscar procesos hijo inesperados.
  - **Contención:** eliminar Axis 1.x del classpath; deshabilitar Legacy API en N-central; segmentar servidores.
  - **Verificación:** confirmar ausencia de JARs de Axis 1.x; validar que no haya actividad SOAP anómala.

### 5.11 CVE-2026-40217 — BerriAI LiteLLM (Unprotected Alternate Channel / RCE)

- **En simple:** Una vulnerabilidad en LiteLLM permite RCE via bytecode rewriting en el endpoint /guardrails/test_custom_code. Requiere admin key de LiteLLM. El contenedor default corre como root. CISA la añadió al KEV el 18/11 con deadline 02/12.
- **CVE:** CVE-2026-40217 · **CVSS:** 8.8 (Alta) · **CWE:** CWE-470 o CWE-94 · **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1078 (Valid Accounts)
- **Productos afectados:** LiteLLM ≤ 2026-04-08. Afecta proxy server para APIs de LLM. También Red Hat: Ansible Automation Platform, OpenShift AI, OpenShift Lightspeed.
- **Explotación:** activa (KEV el 18/11). Deadline federal 02/12.
- **Mitigación:** actualizar LiteLLM a versión posterior a 2026-04-08; restringir acceso al endpoint /guardrails/test_custom_code.
- **IOCs:** requests POST a /guardrails/test_custom_code; actividad anómala en el contenedor LiteLLM.
- **Sigma:** `title: LiteLLM Guardrails Code Execution Attempt | description: Detects POST requests to /guardrails/test_custom_code endpoint | logsource: web | detection: selection: cs-method: 'POST' AND cs-uri-stem|contains: '/guardrails/test_custom_code' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión LiteLLM; revisar logs de acceso a /guardrails/test_custom_code; buscar procesos hijo inesperados.
  - **Contención:** actualizar LiteLLM; restringir endpoint; rotar claves de API si se sospecha compromiso.
  - **Verificación:** confirmar versión parcheada; validar ausencia de actividad anómala.

### 5.12 CVE-2026-19868 — SonicWall SonicOS SSLVPN (Bypass de SAML MFA)

- **En simple:** Una vulnerabilidad en SonicWall SonicOS permite bypass de SAML MFA en SSLVPN. Un usuario con credenciales válidas para un método de autenticación alternativo puede evadir los controles SAML/MFA. CISA la añadió al KEV el 06/11.
- **CVE:** CVE-2026-19868 · **CVSS:** no confirmado en NVD (CWE-308, use of single-factor authentication) · **EPSS:** no disponible
- **MITRE ATT&CK:** T1078 (Valid Accounts) y T1133 (External Remote Services)
- **Productos afectados:** SonicWall SonicOS (Gen7/Gen8 firewalls) con SSLVPN habilitado. Fix en 7.3.4-7013 / 8.2.3-8015.
- **Explotación:** activa (KEV desde 06/11).
- **Mitigación:** actualizar a 7.3.4-7013 / 8.2.3-8015. Revisar logs de autenticación SSLVPN.
- **IOCs:** autenticaciones SSLVPN exitosas sin MFA; accesos a VPN desde IPs no reconocidas.
- **Sigma:** `title: SonicWall SSLVPN MFA Bypass (CVE-2026-19868) | description: Detects SSLVPN authentications that bypass SAML MFA controls | logsource: firewall | detection: selection: event_type: 'sslvpn_auth' AND mfa_status: 'bypassed' | condition: selection`
- **Playbook:**
  - **Triage:** inventariar firewalls SonicWall con SSLVPN; verificar versión SonicOS; revisar logs de auth SSLVPN.
  - **Contención:** actualizar a 7.3.4-7013/8.2.3-8015; deshabilitar SSLVPN si no se usa; forzar MFA en todos los métodos.
  - **Verificación:** confirmar versión parcheada; validar que MFA se aplique en todos los métodos.

### 5.13 CVE-2026-115731 — Perforce Puppet Enterprise (Deserialización)

- **En simple:** Una vulnerabilidad de deserialización de datos no confiables en el procesamiento de reportes de Puppet Enterprise permite a un cliente con certificado firmado por la CA ejecutar código arbitrario en Puppet Server. CISA la añadió al KEV el 17/11 con deadline 01/12.
- **CVE:** CVE-2026-115731 · **CVSS:** 8.8 (Alta) · **CWE:** CWE-502 (deserialización de datos no confiables) · **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application)
- **Productos afectados:** Puppet Enterprise (versiones vulnerables). Weak autosigning puede permitir a atacantes no autenticados obtener un certificado.
- **Explotación:** activa (KEV el 17/11). Deadline federal 01/12.
- **Mitigación:** actualizar Puppet Enterprise a la versión parcheada. Revisar configuración de autosigning.
- **IOCs:** reportes Puppet anómalos; ejecución de código en Puppet Server; certificados no autorizados.
- **Sigma:** `title: Puppet Enterprise Deserialization Exploit Attempt | description: Detects anomalous Puppet report processing with deserialization patterns | logsource: application | detection: selection: event_type: 'puppet_report' AND payload|contains: 'java.io' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión Puppet Enterprise; revisar logs de reportes; buscar certificados no autorizados.
  - **Contención:** actualizar Puppet Enterprise; deshabilitar weak autosigning; segmentar Puppet Server.
  - **Verificación:** confirmar versión parcheada; validar que no haya actividad anómala.

### 5.14 CVE-2026-114938 — Oracle Java (Java4Shell / Memory Corruption en Base64)

- **En simple:** Una vulnerabilidad de memory corruption en el decoder Base64 del HotSpot JVM permite RCE sin autenticación mediante input especialmente crafted. CISA emitió alerta el 19/11 urging patching inmediato. No está en KEV. Afecta JDKs y runtimes Java, incluyendo los embebidos en apps, contenedores y appliances.
- **CVE:** CVE-2026-114938 · **CVSS:** Crítico (CVSS 3.1) · **CWE:** CWE-119 (improper restriction of operations within the bounds of a memory buffer) · **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application)
- **Productos afectados:** Oracle Java (HotSpot JVM, Base64 decoder). JDKs y runtimes Java en apps, contenedores y appliances.
- **Explotación:** alerta de CISA (19/11). No confirmada en KEV. Puede causar crash de JVM y RCE unauth.
- **Mitigación:** aplicar Oracle November 2026 Critical Security Patch Update. Inventariar JDKs y runtimes Java (incluyendo embebidos). Parchear sistemas expuestos a Internet primero. Como mitigación temporal: validar input Base64, limitar tamaño de input, restringir acceso no autenticado.
- **IOCs:** crashes inesperados de JVM; actividad anómala en procesos Java.
- **Sigma:** `title: Java4Shell Base64 Memory Corruption (CVE-2026-114938) | description: Detects unexpected JVM crashes or anomalous Base64 input patterns | logsource: application | detection: selection: event_type: 'jvm_crash' OR input_size: '>limit' | condition: selection`
- **Playbook:**
  - **Triage:** inventariar JDKs y runtimes Java (incluyendo embebidos en apps/contenedores/appliances); verificar versión instalada.
  - **Contención:** aplicar Oracle CPU noviembre 2026; validar input Base64; limitar tamaño de input; restringir acceso no autenticado.
  - **Verificación:** confirmar versión JDK parcheada; monitorear crashes de JVM post-parche.

---

## 6. Explotación activa y ataques en la calle

- **Cadena vCenter (CVE-2026-59310 + CVE-2026-123890):** actor China-nexus desplegando ransomware mediante directory traversal en Syslog + auth bypass en VMware Directory Service. Parche disponible desde agosto 2026 pero muchos sistemas siguen sin actualizar.
- **Apache Roller (CVE-2026-129448):** deserialización pre-auth en XML-RPC. Explotación confirmada en la calle.
- **Cisco Expressway (CVE-2026-76466):** credenciales hard-coded en database service. RCE como root. Explotación confirmada.
- **SonicWall SonicOS (CVE-2026-19868):** bypass de SAML MFA en SSLVPN. Explotación activa desde 06/11.
- **Synology DSM (CVE-2024-10441):** ransomware C3RB3R activo contra NAS sin parchear.
- **NGINX (CVE-2026-123007):** PoC público en GitHub; explotación confirmada en cloud (AWS, GCP, Azure).
- **Apache Axis (CVE-2026-90436):** explotación confirmada contra Confluence Data Center y N-central (Rapid7, 06/11).
- **Puppet Enterprise (CVE-2026-115731):** explotación activa confirmada. Deserialización en procesamiento de reportes.
- **Outlook (CVE-2026-124194):** zero-day del Patch Tuesday de noviembre; filtración de hash NTLMv2. CISA lo añadió al KEV el 10/11.

---

## 7. Ransomware

- **C3RB3R (Synology DSM):** desplegándose activamente contra NAS Synology sin parchear con CVE-2024-10441. Archivos cifrados con extensión .c3rb3r. Parche disponible desde dic 2024.
- **Ransomware vCenter (actor China-nexus):** desplegado mediante CVE-2026-59310 + CVE-2026-123890. Afecta infraestructura de virtualización (VMs, ESXi hosts). Parche disponible desde agosto 2026.
- No se reportaron otras campañas de ransomware específicas esta semana en las fuentes consultadas.

---

## 8. Otras amenazas (APT, cadenas de explotación)

- **Actor China-nexus:** campaña activa contra vCenter Server usando CVE-2026-59310 + CVE-2026-123890. Despliegue de ransomware en infraestructura de virtualización.
- **PHP2Shell (Check Point):** cadena de ataque activa contra gateways Check Point Gaia (CVE-2026-123039 + CVE-2026-109618/109619). Víctimas israelíes con wiper `destroy_v6`. Seguimiento de la semana anterior.
- **wfx-loot:** campaña dirigida a repositorios GitHub reportada el 15/11. Seguimiento de la semana anterior.
- **Java4Shell (CVE-2026-114938):** alerta de CISA del 19/11. Memory corruption en Base64 decoder del HotSpot JVM. No está en KEV pero CISA urge parcheo inmediato.

---

## 9. Tabla comparativa

| CVE | CVSS | Severidad | Explotación | EPSS | Estado | Acción |
|---|---|---|---|---|---|---|
| CVE-2026-123890 (vCenter auth bypass) | 10.0 | 🔴 Crítica | KEV | N/D | Abierto | Parchear vCenter (VMSA-2026-0006.1) |
| CVE-2026-59310 (vCenter Syslog) | 9.8 | 🔴 Crítica | KEV | N/D | Abierto | Parchear vCenter |
| CVE-2026-129448 (Roller) | 9.8 | 🔴 Crítica | KEV | N/D | Abierto | Actualizar a 6.1.6 |
| CVE-2026-76466 (Expressway) | 9.8 | 🔴 Crítica | KEV | N/D | Abierto | Actualizar a X15.5.20 |
| CVE-2024-10441 (Synology DSM) | 9.8 | 🔴 Crítica | KEV | N/D | Abierto | Parchear DSM |
| CVE-2026-123007 (NGINX) | 9.2 | 🔴 Crítica | PoC público | N/D | Abierto | Actualizar a 1.31.9/1.30.7 |
| CVE-2026-90817 (REDCap) | ~9.8 | 🔴 Crítica | KEV | N/D | Abierto | Actualizar REDCap (deadline vencido) |
| CVE-2026-90436 (Apache Axis) | Crítico | 🔴 Crítica | KEV | N/D | Abierto | Eliminar Axis del classpath (deadline vencido) |
| CVE-2026-114938 (Java4Shell) | Crítico | 🔴 Crítica | Alerta CISA | N/D | Abierto | Parchear JDK |
| CVE-2026-19868 (SonicWall) | — | 🟠 Alta | KEV | N/D | Abierto | Actualizar SonicOS |
| CVE-2026-115731 (Puppet Enterprise) | 8.8 | 🟠 Alta | KEV | N/D | Abierto | Actualizar Puppet Enterprise |
| CVE-2026-40217 (LiteLLM) | 8.8 | 🟠 Alta | KEV | N/D | Abierto | Actualizar LiteLLM |
| CVE-2026-124194 (Outlook) | 6.5 | 🟡 Media | KEV | 1.9% | Abierto | Updates Office noviembre |
| CVE-2025-42884 (SAP NetWeaver EP) | 6.5 | 🟡 Media | KEV | N/D | Abierto | SAP Note 3660969 |

---

## 10. Cobertura y limitaciones

- Diarios disponibles para la semana: 18/11, 20/11, 21/11, 22/11. No se encontraron entradas para 16/11, 17/11 y 19/11 en INFORMES_DIARIOS.md.
- CISA añadió 9 CVEs al KEV del 17 al 20/11 (Puppet Enterprise 17/11, Synology/LiteLLM/Axis/REDCap 18/11, vCenter/Roller/Expressway/SAP 20/11). CVE-2026-123890 (vCenter auth bypass) fue añadido el 20/11 pero no aparecía en los diarios; se incluye aquí con datos de CISA KEV y zeptric.io.
- CVE-2026-114938 (Java4Shell) es alerta de CISA del 19/11, no está en KEV. Se incluye por su criticidad.
- EPSS no disponible para la mayoría de los CVEs de esta semana en las fuentes consultadas.
- CISA no publicó IOCs para los CVEs añadidos al KEV esta semana.
- Fuentes complementarias: VulnCheck KEV, Shadowserver, GitHub Security Advisories consultadas cuando aportaron datos.

---

```json
{"fecha":"2026-11-16","cves":[{"id":"CVE-2026-123890","producto":"VMware vCenter Server (VMware Directory Service)","cvss":10.0,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar vCenter a 9.1.1.0200 / 9.0.2.0200 / 8.0 U3m / 5.2.5 (VMSA-2026-0006.1).","runbook":"1) Inventariar instancias vCenter. 2) Parchear a version fija. 3) Revisar logs de VMware Directory Service. 4) Buscar actividad de ransomware en VMs/ESXi.","epss":null,"iocs":["actividad anomala en vmdir","accesos no autorizados a vCenter","archivos cifrados en VMs/ESXi"],"sigma":"Regla Sigma: deteccion de accesos no autenticados a VMware Directory Service indicativos de bypass de autenticacion.","playbook":{"triage":["Verificar version vCenter instalada.","Revisar logs de VMware Directory Service (vmdir).","Buscar accesos no autorizados a vCenter."],"contencion":["Actualizar vCenter a version fija.","Restringir acceso de red a vmdir.","Segmentar vCenter de la red de produccion."],"verificacion":["Confirmar version vCenter parcheada.","Validar ausencia de accesos anomalos post-parche.","Auditar VMs y ESXi hosts."]}},"id":"CVE-2026-59310","producto":"VMware vCenter Server (Syslog service)","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar vCenter a 9.1.1.0200 / 9.0.2.0200 / 8.0 U3m / 5.2.5 (Broadcom VMSA-2026-0006.1).","runbook":"1) Inventariar instancias vCenter. 2) Parchear a version fija. 3) Revisar logs de Syslog service. 4) Buscar actividad de ransomware en VMs/ESXi.","epss":null,"iocs":["archivos cifrados en VMs/ESXi","procesos inusuales en ESXi","actividad anomala en Syslog service"],"sigma":"Regla Sigma: deteccion de patrones de acceso a archivos anormal en Syslog service de vCenter indicativos de directory traversal.","playbook":{"triage":["Verificar version vCenter instalada.","Revisar logs de Syslog service.","Buscar archivos cifrados o procesos inusuales en ESXi hosts."],"contencion":["Aislar vCenter de la red si se sospecha compromiso.","Deshabilitar acceso remoto innecesario.","Verificar integridad de backups de VMs."],"verificacion":["Confirmar version vCenter parcheada.","Validar ausencia de actividad anomala post-parche.","Auditar VMs y ESXi hosts."]}},{"id":"CVE-2026-129448","producto":"Apache Roller","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a Apache Roller 6.1.6 o deshabilitar XML-RPC.","runbook":"1) Inventariar instancias Apache Roller 4.0.0-6.1.5. 2) Actualizar a 6.1.6 o deshabilitar XML-RPC. 3) Revisar logs de /xmlrpc. 4) Buscar procesos hijo inesperados de JVM.","epss":null,"iocs":["requests HTTP POST a /xmlrpc con vendor extension types","procesos hijo inesperados de JVM de Roller"],"sigma":"Regla Sigma: deteccion de requests HTTP POST a endpoint XML-RPC de Apache Roller con vendor extension types sospechosos.","playbook":{"triage":["Verificar version Roller instalada.","Revisar logs de acceso a /xmlrpc.","Buscar procesos hijo inesperados de la JVM."],"contencion":["Actualizar a 6.1.6 o deshabilitar XML-RPC.","Implementar WAF con firmas.","Segmentar el servidor Roller."],"verificacion":["Confirmar version parcheada.","Validar ausencia de actividad anomala post-parche.","Auditar logs de acceso."]}},{"id":"CVE-2026-76466","producto":"Cisco Expressway Series (Expressway-C/E)","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a Cisco Expressway X15.5.20.","runbook":"1) Inventariar clustered Expressway-C/E devices. 2) Actualizar a X15.5.20. 3) Revisar logs de database service. 4) Buscar RPC commands anomalos.","epss":null,"iocs":["RPC commands anomalos al database service de Expressway","accesos no autorizados a Expressway-C/E"],"sigma":"Regla Sigma: deteccion de RPC commands anomalos al database service de Expressway indicativos de explotacion.","playbook":{"triage":["Inventariar clustered Expressway-C/E devices.","Verificar version instalada.","Revisar logs de database service."],"contencion":["Actualizar a X15.5.20.","Restringir acceso de red al database service.","Segmentar dispositivos Expressway."],"verificacion":["Confirmar version parcheada.","Validar ausencia de RPC commands anomalos post-parche.","Auditar accesos."]}},{"id":"CVE-2024-10441","producto":"Synology DiskStation Manager (DSM)","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar DSM a version fija de la linea correspondiente (7.2.2-72806-1, 7.2.1-69057-6, 7.2-64570-4, 7.1.1-42962-7, 6.2.4-25556-8).","runbook":"1) Inventariar NAS Synology. 2) Parchear DSM. 3) Buscar archivos .c3rb3r y ransom notes. 4) Verificar integridad de backups.","epss":null,"iocs":["archivos con extension .c3rb3r","ransom note tipica de C3RB3R","procesos inusuales en NAS"],"sigma":"Regla Sigma: deteccion de creacion de archivos con extension .c3rb3r o ransom note de C3RB3R en Synology DSM.","playbook":{"triage":["Verificar version DSM instalada.","Buscar archivos .c3rb3r o ransom notes.","Revisar logs de acceso al NAS."],"contencion":["Aislar NAS de la red.","Deshabilitar acceso remoto.","Verificar integridad de backups offline."],"verificacion":["Confirmar version DSM parcheada.","Validar ausencia de archivos cifrados.","Restaurar desde backup limpio si es necesario."]}},{"id":"CVE-2026-123007","producto":"NGINX (HTTP/2)","cvss":9.2,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"PoC publico","parche":"Actualizar a NGINX 1.31.9 (mainline) o 1.30.7 (stable). Mitigacion temporal: deshabilitar HTTP/2.","runbook":"1) Inventariar servidores NGINX con HTTP/2. 2) Parchear a 1.31.9/1.30.7. 3) Revisar logs de acceso HTTP/2. 4) Monitorear trafico HTTP/2 anomalo.","epss":null,"iocs":["trafico HTTP/2 con volumen anomalo de control frames (WINDOW_UPDATE, RST_STREAM)","conexiones HTTP/2 de larga duracion con alto volumen de datos"],"sigma":"Regla Sigma: deteccion de alto volumen de control frames HTTP/2 indicativo de intento de explotacion.","playbook":{"triage":["Inventariar servidores NGINX con HTTP/2 habilitado.","Verificar version instalada.","Revisar logs de acceso HTTP/2."],"contencion":["Parchear a 1.31.9/1.30.7.","Deshabilitar HTTP/2 como mitigacion temporal.","Implementar IPS con firmas."],"verificacion":["Confirmar version parcheada.","Validar que HTTP/2 este deshabilitado si no se parcheo.","Monitorear trafico HTTP/2 post-mitigacion."]}},{"id":"CVE-2026-90817","producto":"Vanderbilt University REDCap","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar REDCap a 16.0.49 / 17.3.10 / 17.4.4.","runbook":"1) Inventariar instancias REDCap >= 13.3.0. 2) Actualizar a version fija. 3) Revisar logs de acceso a __passthru y Data Import. 4) Buscar procesos hijo inesperados.","epss":null,"iocs":["requests HTTP anomalos a endpoints __passthru con parametros de file-path o stream manipulados","procesos hijo inesperados del servidor web"],"sigma":"Regla Sigma: deteccion de requests HTTP a endpoint __passthru de REDCap con parametros de file-path sospechosos.","playbook":{"triage":["Verificar version REDCap instalada.","Revisar logs de acceso a __passthru y Data Import.","Buscar procesos hijo inesperados del servidor web."],"contencion":["Actualizar REDCap.","Restringir acceso a encuestas publicas si no son necesarias.","Segmentar el servidor REDCap."],"verificacion":["Confirmar version parcheada.","Validar ausencia de actividad anomala en logs post-parche."]}},"id":"CVE-2026-90436","producto":"Apache Axis 1.x (EOL)","cvss":10.0,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"No hay parche (EOL). Eliminar Axis 1.x del classpath o actualizar la app host (Confluence, N-central).","runbook":"1) Buscar JARs de org.apache.axis:axis en classpaths. 2) Eliminar Axis 1.x del classpath. 3) Revisar logs de acceso SOAP. 4) Buscar procesos hijo inesperados de la JVM.","epss":null,"iocs":["requests SOAP con exceptionName sospechoso en el body","procesos hijo inesperados de la JVM de Confluence/N-central"],"sigma":"Regla Sigma: deteccion de requests SOAP conteniendo elemento exceptionName dirigidos a Axis 1.x.","playbook":{"triage":["Buscar JARs de org.apache.axis:axis en classpaths.","Revisar logs de acceso SOAP en Confluence/N-central.","Buscar procesos hijo inesperados de la JVM."],"contencion":["Eliminar Axis 1.x del classpath.","Deshabilitar Legacy API en N-central.","Segmentar servidores afectados."],"verificacion":["Confirmar ausencia de JARs de Axis 1.x.","Validar que no haya actividad SOAP anomala post-mitigacion."]}},"id":"CVE-2026-40217","producto":"BerriAI LiteLLM","cvss":8.8,"severidad":"Alta","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar LiteLLM a version posterior a 2026-04-08. Restringir acceso a /guardrails/test_custom_code.","runbook":"1) Inventariar instancias LiteLLM. 2) Actualizar a version post 2026-04-08. 3) Restringir endpoint /guardrails/test_custom_code. 4) Revisar logs de acceso.","epss":null,"iocs":["requests POST a /guardrails/test_custom_code","actividad anomala en el contenedor LiteLLM"],"sigma":"Regla Sigma: deteccion de requests POST a endpoint /guardrails/test_custom_code de LiteLLM.","playbook":{"triage":["Verificar version LiteLLM instalada.","Revisar logs de acceso a /guardrails/test_custom_code.","Buscar procesos hijo inesperados en el contenedor."],"contencion":["Actualizar LiteLLM.","Restringir endpoint.","Rotar claves de API de LLM si se sospecha compromiso."],"verificacion":["Confirmar version parcheada.","Validar ausencia de actividad anomala post-parche.","Auditar claves de API."]}},{"id":"CVE-2026-19868","producto":"SonicWall SonicOS SSLVPN","cvss":null,"severidad":"Alta","mitre":{"id":"T1078","nombre":"Valid Accounts"},"explotacion":"KEV","parche":"Actualizar a SonicOS 7.3.4-7013 / 8.2.3-8015.","runbook":"1) Inventariar firewalls SonicWall con SSLVPN. 2) Actualizar SonicOS. 3) Revisar logs de auth SSLVPN. 4) Forzar MFA en todos los metodos de auth.","epss":null,"iocs":["autenticaciones SSLVPN exitosas sin MFA","accesos a VPN desde IPs no reconocidas"],"sigma":"Regla Sigma: deteccion de autenticaciones SSLVPN que omiten controles SAML MFA.","playbook":{"triage":["Inventariar firewalls SonicWall con SSLVPN.","Verificar version SonicOS.","Revisar logs de auth SSLVPN."],"contencion":["Actualizar a 7.3.4-7013/8.2.3-8015.","Deshabilitar SSLVPN si no se usa.","Forzar MFA en todos los metodos de auth."],"verificacion":["Confirmar version parcheada.","Validar que MFA se aplique en todos los metodos.","Auditar accesos VPN."]}},{"id":"CVE-2026-115731","producto":"Perforce Puppet Enterprise","cvss":8.8,"severidad":"Alta","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar Puppet Enterprise a version parcheada. Revisar configuracion de autosigning.","runbook":"1) Verificar version Puppet Enterprise. 2) Actualizar a version parcheada. 3) Revisar logs de reportes. 4) Buscar certificados no autorizados.","epss":null,"iocs":["reportes Puppet anomalos","ejecucion de codigo en Puppet Server","certificados no autorizados"],"sigma":"Regla Sigma: deteccion de procesamiento de reportes Puppet anomalo con patrones de deserializacion.","playbook":{"triage":["Verificar version Puppet Enterprise.","Revisar logs de reportes.","Buscar certificados no autorizados."],"contencion":["Actualizar Puppet Enterprise.","Deshabilitar weak autosigning.","Segmentar Puppet Server."],"verificacion":["Confirmar version parcheada.","Validar que no haya actividad anomala."]}},"id":"CVE-2026-124194","producto":"Microsoft Outlook / Microsoft 365 Apps","cvss":6.5,"severidad":"Media","mitre":{"id":"T1187","nombre":"Forced Authentication"},"explotacion":"KEV","parche":"Instalar updates de noviembre 2026 de Office (builds 16.0.14334.21048 / 16.0.17932.21140).","runbook":"1) Verificar version de Office instalada. 2) Aplicar parche. 3) Bloquear SMB/NTLM saliente. 4) Revisar logs de conexiones SMB anomalas.","epss":0.019,"iocs":[],"sigma":"Deteccion de conexiones SMB/NTLM salientes inusuales desde procesos de Outlook.","playbook":{"triage":["Verificar version de Office.","Confirmar si el usuario abrio archivos de mensaje inesperados.","Buscar conexiones SMB salientes."],"contencion":["Aplicar parche.","Bloquear SMB/NTLM saliente.","Aislar endpoints sospechosos."],"verificacion":["Confirmar build de Office.","Revisar logs de firewall/proxy.","Validar que no haya hashes NTLM filtrados."]}},{"id":"CVE-2025-42884","producto":"SAP NetWeaver Enterprise Portal","cvss":6.5,"severidad":"Media","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Aplicar SAP Note 3660969.","runbook":"1) Inventariar SAP NetWeaver Enterprise Portal. 2) Aplicar SAP Note 3660969. 3) Revisar logs de portal. 4) Buscar errores SQL/JNDI anomalos.","epss":null,"iocs":["requests HTTP con parametros SQL/JNDI sospechosos en el portal SAP NetWeaver","errores SQL en logs del portal"],"sigma":"Regla Sigma: deteccion de requests HTTP a SAP NetWeaver Enterprise Portal con parametros SQL sospechosos.","playbook":{"triage":["Verificar version SAP NetWeaver EP instalada.","Revisar logs de portal para SQL/JNDI injection.","Buscar errores SQL anomalos."],"contencion":["Aplicar SAP Note 3660969.","Restringir acceso al portal.","Segmentar el servidor SAP."],"verificacion":["Confirmar nota SAP aplicada.","Validar ausencia de SQL injection post-parche.","Auditar datos del portal."]}},{"id":"CVE-2026-114938","producto":"Oracle Java (HotSpot JVM Base64 decoder)","cvss":null,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"Alerta CISA (no KEV)","parche":"Aplicar Oracle November 2026 Critical Security Patch Update. Validar input Base64, limitar tamano de input, restringir acceso no autenticado como mitigacion temporal.","runbook":"1) Inventariar JDKs y runtimes Java (incluyendo embebidos). 2) Aplicar Oracle CPU noviembre 2026. 3) Validar input Base64. 4) Monitorear crashes de JVM.","epss":null,"iocs":["crashes inesperados de JVM","actividad anomala en procesos Java"],"sigma":"Regla Sigma: deteccion de crashes inesperados de JVM o patrones de input Base64 anomalos.","playbook":{"triage":["Inventariar JDKs y runtimes Java (incluyendo embebidos en apps/contenedores/appliances).","Verificar version instalada."],"contencion":["Aplicar Oracle CPU noviembre 2026.","Validar input Base64.","Limitar tamano de input.","Restringir acceso no autenticado."],"verificacion":["Confirmar version JDK parcheada.","Monitorear crashes de JVM post-parche."]}}],"otras_amenazas":[{"nombre":"Actor China-nexus (campana vCenter)","tipo":"APT","resumen":"Actor China-nexus desplegando ransomware mediante CVE-2026-59310 + CVE-2026-123890 contra infraestructura de virtualizacion (vCenter, VMs, ESXi)."},{"nombre":"C3RB3R (ransomware Synology)","tipo":"ransomware","resumen":"Ransomware C3RB3R desplegandose contra NAS Synology sin parchear con CVE-2024-10441. Archivos cifrados con extension .c3rb3r."},{"nombre":"PHP2Shell (Check Point)","tipo":"campana","resumen":"Cadena de ataque activa contra gateways Check Point Gaia (CVE-2026-123039 + CVE-2026-109618/109619). Victimas israelies con wiper destroy_v6. Seguimiento de semana anterior."},{"nombre":"wfx-loot (GitHub)","tipo":"campana","resumen":"Campana dirigida a repositorios GitHub reportada el 15/11. Seguimiento de semana anterior."},{"nombre":"Java4Shell (CVE-2026-114938)","tipo":"campana","resumen":"Alerta de CISA del 19/11. Memory corruption en Base64 decoder del HotSpot JVM. No esta en KEV pero CISA urge parcheo inmediato."}]}
```

**Ruta del documento:** `F:\Repositorios\CyberThreatIntelligence\INFORMES_SEMANALES.md`