# 📊 Informe CTI Diario - 2026-11-27

**Fecha del informe:** 28/11/2026  **Cobertura:** novedades del viernes 27 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) 🟠 Alta (7-8.9) 🟡 Media (4-6.9) 🟢 Baja (<4).
**Siglas:** KEV = Known Exploited Vulnerabilities (catálogo CISA de vulnerabilidades con explotación confirmada); CVSS = Common Vulnerability Scoring System (puntaje de severidad 0-10); CWE = Common Weakness Enumeration (tipo de debilidad); PoC = Proof of Concept (código de prueba de explotación); EOL = End of Life (fin de soporte); APT = Advanced Persistent Threat (amenaza persistente avanzada); EPSS = Exploit Prediction Scoring System (probabilidad de explotación en 30 días); RCE = Remote Code Execution; SSRF = Server-Side Request Forgery; LPE = Local Privilege Escalation; WFS = Web Feature Service.

> 🔴 **Resumen en 30 segundos:** CISA añadió 3 CVE al KEV el 27/11: dos de GeoServer (CVE-2026-120705, CVE-2026-129243) y uno de Drupal Core (CVE-2026-136716), todos con plazo FCEB 30/11 y explotación activa. Los de GeoServer se encadenan con otros CVE para lograr RCE no autenticado.

## 1. Turno de vigilancia — qué atender primero

### Top 5 atacar hoy

| # | CVE | Severidad | Acción | Tiempo est. |
|---|---|---|---|---|
| 1 | CVE-2026-136716 (Drupal Core) | 🔴 9.8 | Actualizar Drupal a 10.6.23/11.3.23/11.4.14; ejecutar update.php; asegurar installer. | 1-2 h |
| 2 | CVE-2026-129243 (GeoServer) | 🟠 8.8 | Actualizar GeoServer a 2.27.7/2.28.6/3.0.2; revisar stored queries. | 1-2 h |
| 3 | CVE-2026-120705 (GeoServer) | 🟠 8.6 | Mismo parche que arriba; restringir WFS anónimo. | 1-2 h |
| 4 | CVE-2026-137481 (Traefik) | 🔴 9.3 | Actualizar Traefik a 2.11.62/3.7.18. | 1 h |
| 5 | CVE-2026-137738 (RealVNC Viewer) | 🔴 9.8 | Actualizar RealVNC Viewer a versión corregida. | 1 h |

### Prioridades

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 - Drupal Core (CVE-2026-136716) | RCE vía installer expuesto; actualizar + update.php + asegurar install.php. |
| 2 | 🟠 P1 - GeoServer (CVE-2026-120705/129243) | Explotación activa, cadena RCE no autenticado; parchear y revisar stored queries. |
| 3 | 🔴 P2 - Traefik (CVE-2026-137481) | Bypass de middleware de autenticación; actualizar. |
| 4 | 🔴 P2 - RealVNC Viewer (CVE-2026-137738) | Out-of-bounds write; actualizar. |
| 5 | 🟢 General | Vigilar NVD/KEV; verificar Shadowserver para explotación en Internet. |

> ℹ️ CISA añadió los 3 CVE al KEV el 27/11 con plazo FCEB 30/11. GeoServer parche disponible desde octubre (3.0.2/2.28.6/2.27.7). Drupal publicó SA-CORE-2026-019 el 25/11.

## 2. Recomendaciones SOC L1

1. 🔴 **Drupal Core (CVE-2026-136716):** actualizar a 10.6.23/11.3.23/11.4.14; ejecutar update.php; asegurar install.php; revisar logs de la cuenta 1.
2. 🟠 **GeoServer (CVE-2026-120705/129243):** actualizar a 3.0.2/2.28.6/2.27.7; revisar carpetas wfs/query por stored queries no creados; cambiar contraseñas admin si hay exposición.
3. 🔴 **Traefik (CVE-2026-137481):** actualizar a 2.11.62/3.7.18; revisar middlewares de autenticación.
4. 🔴 **RealVNC Viewer (CVE-2026-137738):** actualizar a versión corregida.
5. 🟢 **General:** monitorear NVD/KEV y Shadowserver para nuevas inclusiones.

## 3. Recomendaciones SOC L2

- Hardening: restringir acceso a install.php en Drupal; deshabilitar WFS anónimo en GeoServer; restringir middlewares de Traefik.
- Threat hunting: buscar stored queries no autorizados en GeoServer; accesos a install.php en Drupal; requests con service param prefijado.
- Tuning: crear reglas Sigma/YARA para patrones de XML injection en WFS y bypass de autorización por prefijo.
- Automatización: integrar KEV en flujo de parcheo con alertas de plazo 3 días.
- Mejora de proceso: inventario de instancias GeoServer/Drupal/Traefik; verificación de versiones EOL.

## 4. Resumen ejecutivo

- CISA añadió 3 CVE al KEV el 27/11: 2 de GeoServer y 1 de Drupal Core, todos con plazo FCEB 30/11.
- GeoServer: cadena de RCE no autenticado combinando 5 CVE; parche disponible desde octubre.
- Drupal: follow-up de SA-CORE-2026-017; sitios en estado de instalación inusual siguen vulnerables.
- Traefik (CVE-2026-137481, 9.3) y RealVNC Viewer (CVE-2026-137738, 9.8) publicados el 27/11 sin explotación confirmada.

## 5. Hallazgos

### En simple

GeoServer es un servidor de mapas de código abierto muy usado por gobiernos y utilities. Dos de sus fallos (lectura de archivos + bypass de autorización) se explotan activamente y se encadenan con otros para lograr ejecución remota de código sin autenticación; el riesgo es compromiso total del servidor y robo de credenciales/configuración. Drupal Core es un CMS muy extendido; su instalador, si queda expuesto en estado de instalación inusual, permite takeover del sitio; el riesgo es pérdida total del sitio y datos. Traefik es un reverse proxy con un bypass de middleware de autenticación; RealVNC Viewer tiene un out-of-bounds write; ambos críticos pero sin explotación confirmada en la calle.

### Detalle técnico

**CVE-2026-120705 — OSGeo GeoServer XML Injection**
- **CVE:** CVE-2026-120705 | **CVSS 3.1:** 8.6 (High) | **CWE:** CWE-91, CWE-611, CWE-918 | **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application)
- **Productos afectados:** GeoServer 2.27.6 y anteriores, 2.28.0-2.28.5, 3.0.0-3.0.1; GeoTools <33.7/34.6/35.2
- **Explotación:** KEV (añadido 27/11), explotación activa confirmada (watchTowr honeypot), PoC público (Nuclei template)
- **Impacto:** lectura arbitraria de archivos locales + SSRF; encadenado con otros CVE para RCE no autenticado
- **Mitigación:** actualizar a 2.27.7/2.28.6/3.0.2 y GeoTools 33.7/34.6/35.2; restringir WFS anónimo; bloquear service param con slash en proxy
- **IOCs:** requests WFS GetFeatureById con ID malicioso; stored queries en data directory wfs/query
- **Sigma:** `selection: cs-uri-query|contains: 'GetFeatureById' AND cs-uri-query|contains: '..'` (detección simplificada)
- **Playbook:** Triage: verificar versión GeoServer y exposición WFS. Contención: restringir WFS anónimo, bloquear slash en service param. Verificación: revisar wfs/query por stored queries no creados, cambiar contraseñas admin.

**CVE-2026-129243 — OSGeo GeoServer Incorrect Authorization**
- **CVE:** CVE-2026-129243 | **CVSS 3.1:** 8.8 (High) | **CWE:** CWE-863 | **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application)
- **Productos afectados:** GeoServer 2.27.6 y anteriores, 2.28.0-2.28.5, 3.0.0-3.0.1
- **Explotación:** KEV (añadido 27/11), explotación activa confirmada
- **Impacto:** bypass de service-level security rules vía prefixed service param (x/WFS), permite crear/deletar stored queries
- **Mitigación:** mismo parche (2.27.7/2.28.6/3.0.2); bloquear service param con slash en proxy
- **IOCs:** requests con service param prefijado (x/WFS); stored queries no autorizados
- **Sigma:** `selection: cs-uri-query|contains: 'service=x/'`
- **Playbook:** Triage: verificar versión y reglas de service security. Contención: bloquear slash en service param. Verificación: auditar stored queries y logs de acceso.

**CVE-2026-136716 — Drupal Core Improper Check**
- **CVE:** CVE-2026-136716 | **CVSS 3.1:** 9.8 (Critical, CISA-ADP) | **CWE:** CWE-754 | **EPSS:** 1.2%
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application)
- **Productos afectados:** Drupal <10.6.23, 11.0.0-11.3.23, 11.4.0-11.4.14
- **Explotación:** KEV (añadido 27/11), explotación activa confirmada (Drupal.org, NHS England)
- **Impacto:** takeover del sitio vía installer expuesto en estado de instalación inusual/incompleto
- **Mitigación:** actualizar a 10.6.23/11.3.23/11.4.14; ejecutar update.php; asegurar install.php
- **IOCs:** accesos a install.php; cambios en cuenta 1 (user 1)
- **Sigma:** `selection: cs-uri-stem|endswith: '/install.php' AND cs-status: 200`
- **Playbook:** Triage: verificar versión Drupal y exposición de install.php. Contención: bloquear install.php en WAF/proxy. Verificación: ejecutar update.php; verificar cuenta 1; revisar logs.

**CVE-2026-137481 — Traefik Auth Middleware Bypass**
- **CVE:** CVE-2026-137481 | **CVSS:** 9.3 | **EPSS:** no disponible
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application)
- **Productos afectados:** Traefik <2.11.62, <3.7.18
- **Explotación:** PoC (VulnCheck), sin explotación confirmada en KEV
- **Impacto:** bypass de middlewares de autenticación vía paths como /..;x/
- **Mitigación:** actualizar a 2.11.62/3.7.18
- **IOCs:** requests con paths /..;x/
- **Sigma:** `selection: cs-uri-stem|contains: '/..;'`
- **Playbook:** Triage: verificar versión Traefik. Contención: actualizar. Verificación: probar middleware con path malicioso.

**CVE-2026-137738 — RealVNC Viewer Out-of-bounds Write**
- **CVE:** CVE-2026-137738 | **CVSS:** 9.8 | **EPSS:** no disponible
- **MITRE ATT&CK:** T1203 (Exploitation for Client Execution)
- **Productos afectados:** RealVNC Viewer 8.x
- **Explotación:** sin explotación confirmada
- **Impacto:** out-of-bounds write, posible RCE en cliente
- **Mitigación:** actualizar a versión corregida
- **IOCs:** no disponibles
- **Sigma:** no disponible
- **Playbook:** Triage: verificar versión RealVNC. Contención: actualizar. Verificación: confirmar versión parcheada.

## 6. Contexto de seguimiento

- CVE-2026-136716 (Drupal): nuevo en KEV 27/11, follow-up de CVE-2026-132165 (SA-CORE-2026-017); sin cambios adicionales.
- CVE-2026-120705/129243 (GeoServer): nuevos en KEV 27/11; explotación activa desde 25/11 (watchTowr honeypot).
- CVE-2026-137481 (Traefik): publicado 27/11, sin KEV.
- CVE-2026-137738 (RealVNC): publicado 27/11, sin KEV.

## 7. Cobertura y limitaciones

- Fuentes: CISA KEV, NVD, fabricantes (GeoServer, Drupal, Traefik, RealVNC), CERTs (NHS England, Canadian Centre).
- Limitaciones: EPSS no disponible para CVE-2026-120705, 129243, 137481, 137738; NVD sin score para GeoServer (CVSS de GeoServer/CISA).
- Shadowserver y VulnCheck no consultados directamente en esta corrida.

```json
{"fecha":"2026-11-27","cves":[{"id":"CVE-2026-136716","producto":"Drupal Core","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a 10.6.23/11.3.23/11.4.14 + update.php + asegurar install.php","runbook":"1. Verificar versión Drupal y exposición de install.php. 2. Actualizar core. 3. Ejecutar update.php. 4. Asegurar install.php y revisar cuenta 1.","epss":0.012,"iocs":["accesos a install.php","cambios en cuenta 1 (user 1)"],"sigma":"selection: cs-uri-stem|endswith: '/install.php' AND cs-status: 200","playbook":{"triage":["Verificar versión Drupal y exposición de install.php"],"contencion":["Bloquear install.php en WAF/proxy"],"verificacion":["Ejecutar update.php","Verificar cuenta 1","Revisar logs"]}}, {"id":"CVE-2026-129243","producto":"GeoServer","cvss":8.8,"severidad":"Alta","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a 2.27.7/2.28.6/3.0.2; bloquear service param con slash en proxy","runbook":"1. Verificar versión GeoServer. 2. Actualizar. 3. Bloquear slash en service param. 4. Auditar stored queries.","epss":null,"iocs":["requests con service param prefijado (x/WFS)","stored queries no autorizados"],"sigma":"selection: cs-uri-query|contains: 'service=x/'","playbook":{"triage":["Verificar versión y reglas de service security"],"contencion":["Bloquear slash en service param"],"verificacion":["Auditar stored queries y logs de acceso"]}}, {"id":"CVE-2026-120705","producto":"GeoServer","cvss":8.6,"severidad":"Alta","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a 2.27.7/2.28.6/3.0.2 + GeoTools 33.7/34.6/35.2; restringir WFS anónimo","runbook":"1. Verificar versión GeoServer y exposición WFS. 2. Actualizar. 3. Restringir WFS anónimo. 4. Revisar wfs/query.","epss":null,"iocs":["requests WFS GetFeatureById con ID malicioso","stored queries en data directory wfs/query"],"sigma":"selection: cs-uri-query|contains: 'GetFeatureById' AND cs-uri-query|contains: '..'","playbook":{"triage":["Verificar versión GeoServer y exposición WFS"],"contencion":["Restringir WFS anónimo","Bloquear slash en service param"],"verificacion":["Revisar wfs/query por stored queries no creados","Cambiar contraseñas admin"]}}, {"id":"CVE-2026-137481","producto":"Traefik","cvss":9.3,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"PoC","parche":"Actualizar a 2.11.62/3.7.18","runbook":"1. Verificar versión Traefik. 2. Actualizar. 3. Probar middleware con path malicioso.","epss":null,"iocs":["requests con paths /..;x/"],"sigma":"selection: cs-uri-stem|contains: '/..;'","playbook":{"triage":["Verificar versión Traefik"],"contencion":["Actualizar"],"verificacion":["Probar middleware con path malicioso"]}}, {"id":"CVE-2026-137738","producto":"RealVNC Viewer","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1203","nombre":"Exploitation for Client Execution"},"explotacion":"sin explotacion confirmada","parche":"Actualizar a versión corregida","runbook":"1. Verificar versión RealVNC. 2. Actualizar. 3. Confirmar versión parcheada.","epss":null,"iocs":[],"sigma":"no disponible","playbook":{"triage":["Verificar versión RealVNC"],"contencion":["Actualizar"],"verificacion":["Confirmar versión parcheada"]}}],"otras_amenazas":[]}
```
path: C:/Users/Seba/AppData/Local/Temp/opencode/new_entry.md

# 📋 Informe CTI Diario - 2026-11-26

**Fecha del informe:** 27/11/2026  **Cobertura:** novedades del jueves 26 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) 🟠 Alta (7-8.9) 🟡 Media (4-6.9) 🟢 Baja (<4).
**Siglas:** KEV = Known Exploited Vulnerabilities (catálogo CISA de vulnerabilidades con explotación confirmada); CVSS = Common Vulnerability Scoring System (puntaje de severidad 0-10); CWE = Common Weakness Enumeration (tipo de debilidad); PoC = Proof of Concept (código de prueba de explotación); EOL = End of Life (fin de soporte); APT = Advanced Persistent Threat (amenaza persistente avanzada); EPSS = Exploit Prediction Scoring System (probabilidad de explotación en 30 días); SSRF = Server-Side Request Forgery; RCE = Remote Code Execution; LPE = Local Privilege Escalation.

> ℹ️ **Resumen en 30 segundos:** CISA mantuvo activo el plazo del 26/11 para dos CVE recién añadidos al KEV el 23/11: CVE-2026-134624 (N-able MailAssure, SSRF, CVSS 9.1) y CVE-2026-133968 (MONAI Label, deserialización insegura, CVSS 9.8, RCE). Además, NVD publicó CVE-2026-133951 (WordPress Simple Membership, escalación de privilegios, CVSS 9.8) y CVE-2026-137498/137499 (Budibase, prototype pollution y bypass de autenticación).

## 1. Turno de vigilancia — qué atender primero

### Top 5 atacar hoy

| # | CVE | Severidad | Acción | Tiempo est. |
|---|---|---|---|---|
| 1 | CVE-2026-133968 (MONAI Label) | 🔴 9.8 | Actualizar MONAI Label; aislar servicio de etiquetado. | 1-2 h |
| 2 | CVE-2026-134624 (N-able MailAssure) | 🟠 9.1 | Actualizar MailAssure; restringir API a IPs de confianza. | 1-2 h |
| 3 | CVE-2026-133951 (WordPress Simple Membership) | 🔴 9.8 | Actualizar plugin a versión corregida; revisar usuarios. | 1 h |
| 4 | CVE-2026-137499 (Budibase) | 🔴 9.8 | Actualizar Budibase a 4.0.0+. | 1-2 h |
| 5 | CVE-2026-137498 (Budibase) | 🟠 8.x | Actualizar Budibase; validar filtros MongoDB. | 1-2 h |

### Prioridades

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 - MONAI Label (CVE-2026-133968) | Deserialización insegura con RCE; actualizar y aislar el servicio. |
| 2 | 🟠 P1 - N-able MailAssure (CVE-2026-134624) | SSRF en API autenticada; actualizar y restringir acceso. |
| 3 | 🔴 P2 - WordPress Simple Membership (CVE-2026-133951) | Escalación de privilegios; actualizar plugin y auditar usuarios. |
| 4 | 🟠 P2 - Budibase (CVE-2026-137498/137499) | Prototype pollution y bypass de auth; actualizar a 4.0.0+. |
| 5 | 🟢 General | Vigilar NVD/KEV; verificar Shadowserver para explotación en Internet. |

> ℹ️ CISA añadió ambos CVE al KEV el 23/11 con plazo FCEB 26/11. NVD publicó los CVE de WordPress y Budibase el 26/11.

## 2. Recomendaciones SOC L1

1. 🔴 **MONAI Label (CVE-2026-133968):** actualizar a versión corregida; aislar el servicio de etiquetado; revisar logs de deserialización y ejecución de comandos.
2. 🟠 **N-able MailAssure (CVE-2026-134624):** actualizar; restringir API a IPs de confianza; revisar logs de peticiones SSRF.
3. 🔴 **WordPress Simple Membership (CVE-2026-133951):** actualizar plugin; auditar usuarios y roles creados recientemente.
4. 🟠 **Budibase (CVE-2026-137498/137499):** actualizar a 4.0.0+; validar filtros MongoDB y cuentas globales sin contraseña.

## 3. Recomendaciones SOC L2

- Hardening: restringir endpoints de API y servicios de etiquetado a redes de confianza.
- Threat hunting: buscar IOCs de deserialización y SSRF en logs de aplicación.
- Tuning: crear reglas de detección para patrones de deserialización insegura y SSRF.
- Automatización: integrar KEV en el flujo de parcheo con alertas de plazo.
- Mejora de proceso: inventario de plugins WordPress y versiones de Budibase.

## 4. Resumen ejecutivo

- CISA mantuvo el plazo del 26/11 para dos CVE KEV (MONAI Label y N-able MailAssure).
- NVD publicó CVE-2026-133951 (WordPress Simple Membership) con CVSS 9.8.
- NVD publicó CVE-2026-137498/137499 (Budibase) con prototype pollution y bypass de auth.
- Recomendación: parchear MONAI Label y MailAssure de inmediato; actualizar plugins WordPress y Budibase.

## 5. Hallazgos

### En simple

MONAI Label es una herramienta de etiquetado de imágenes médicas que tiene una falla de deserialización insegura que permite ejecución remota de código; MONAI es el proveedor y el riesgo es total control del servidor. N-able MailAssure es un gateway de correo con una falla SSRF que permite a usuarios autenticados de la API sondear servicios internos; el riesgo es fuga de información y movimiento lateral. WordPress Simple Membership es un plugin de membresías con escalación de privilegios que permite a usuarios sin privilegios tomar control; el riesgo es compromiso total del sitio. Budibase es una plataforma low-code con prototype pollution y bypass de autenticación que permite tomar control de cuentas globales; el riesgo es pérdida de datos y control.

### Detalle técnico

**CVE-2026-133968 — MONAI Label (deserialización insegura → RCE)**
- CVSS: 9.8 (Crítica). CWE: CWE-502 (Deserialization of Untrusted Data).
- MITRE ATT&CK: T1190 (Exploit Public-Facing Application); T1059 (Command and Scripting Interpreter).
- Productos: MONAI Label (versiones afectadas según fabricante). Explotación: KEV (añadido 23/11/2026, plazo 26/11).
- Impacto: ejecución remota de código sin autenticación en el servicio de etiquetado.
- Mitigación: actualizar a versión corregida; aislar servicio; deshabilitar deserialización insegura.

**CVE-2026-134624 — N-able MailAssure (SSRF)**
- CVSS: 9.1 (Alta). CWE: CWE-918 (Server-Side Request Forgery).
- MITRE ATT&CK: T1190 (Exploit Public-Facing Application); T1090 (Proxy).
- Productos: N-able MailAssure (versiones afectadas). Explotación: KEV (añadido 23/11/2026, plazo 26/11).
- Impacto: sondeo de servicios internos y envío de datos elaborados.
- Mitigación: actualizar; restringir API a IPs de confianza.

**CVE-2026-133951 — WordPress Simple Membership (escalación de privilegios)**
- CVSS: 9.8 (Crítica). CWE: CWE-269 (Improper Privilege Management).
- MITRE ATT&CK: T1078 (Valid Accounts); T1136 (Create Account).
- Productos: Simple Membership plugin ≤ 4.8.9. Explotación: PoC/NVD (publicado 26/11/2026).
- Impacto: escalación de privilegios a administrador.
- Mitigación: actualizar plugin; auditar usuarios.

**CVE-2026-137498/137499 — Budibase (prototype pollution + bypass de auth)**
- CVSS: 8.x-9.8. CWE: CWE-1321 (Prototype Pollution), CWE-287 (Improper Authentication).
- MITRE ATT&CK: T1190 (Exploit Public-Facing Application); T1078 (Valid Accounts).
- Productos: Budibase Server 3.39.18 a 3.41.x (antes de 4.0.0). Explotación: NVD (publicado 26/11/2026).
- Impacto: borrado de filtros MongoDB y toma de control de cuentas globales.
- Mitigación: actualizar a 4.0.0+.

### Tabla comparativa

| CVE | Producto | CVSS | Explotación | Acción |
|---|---|---|---|---|
| CVE-2026-133968 | MONAI Label | 9.8 | KEV | Actualizar + aislar |
| CVE-2026-134624 | N-able MailAssure | 9.1 | KEV | Actualizar + restringir API |
| CVE-2026-133951 | WP Simple Membership | 9.8 | PoC | Actualizar plugin |
| CVE-2026-137498/99 | Budibase | 8.x-9.8 | NVD | Actualizar a 4.0.0+ |

## 6. Contexto de seguimiento

- CVE-2026-133323 y otros Mitel MiCollab: sin novedades desde 25/11.
- CVE-2026-133469 Xen Orchestra: sin novedades.
- CVE-2026-103406 HPE Juniper: sin novedades.
- CVE-2026-129713 Danfoss: sin parche, mitigaciones vigentes.

## 7. Cobertura y limitaciones

Cobertura: CISA KEV, NVD, MITRE ATT&CK. Limitaciones: sin acceso a feeds privados; EPSS estimado; IOCs no disponibles para estos CVE en fuentes públicas.

## META DEL PANEL (no se muestra en la tarjeta)

```json
{"fecha":"2026-11-26","cves":[{"id":"CVE-2026-133968","producto":"MONAI Label","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar MONAI Label y aislar servicio","runbook":"1) Identificar instancias MONAI Label 2) Aplicar parche del fabricante 3) Aislar servicio de etiquetado 4) Revisar logs de deserializacion","epss":null,"iocs":[],"sigma":"detection: deserialization payload in MONAI Label requests","playbook":{"triage":["Verificar version de MONAI Label","Revisar logs de deserializacion"],"contencion":["Aislar servicio de etiquetado","Bloquear trafico no confiable"],"verificacion":["Confirmar version parcheada","Validar ausencia de ejecucion de comandos"]}}, {"id":"CVE-2026-134624","producto":"N-able MailAssure","cvss":9.1,"severidad":"Alta","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar MailAssure y restringir API","runbook":"1) Identificar instancias MailAssure 2) Aplicar parche 3) Restringir API a IPs de confianza 4) Revisar logs SSRF","epss":null,"iocs":[],"sigma":"detection: SSRF patterns in MailAssure API logs","playbook":{"triage":["Verificar version de MailAssure","Revisar logs de API"],"contencion":["Restringir API a IPs confiables","Bloquear destinos internos"],"verificacion":["Confirmar version parcheada","Validar ausencia de SSRF"]}}, {"id":"CVE-2026-133951","producto":"WordPress Simple Membership","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1078","nombre":"Valid Accounts"},"explotacion":"PoC","parche":"Actualizar plugin Simple Membership","runbook":"1) Identificar sitios con plugin 2) Actualizar plugin 3) Auditar usuarios y roles 4) Revisar logs de creacion de cuentas","epss":null,"iocs":[],"sigma":"detection: privilege escalation in Simple Membership plugin","playbook":{"triage":["Verificar version del plugin","Auditar usuarios recientes"],"contencion":["Actualizar plugin","Deshabilitar registro si no es necesario"],"verificacion":["Confirmar version corregida","Validar roles de usuario"]}}, {"id":"CVE-2026-137498","producto":"Budibase Server","cvss":8.0,"severidad":"Alta","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"sin explotacion confirmada","parche":"Actualizar Budibase a 4.0.0+","runbook":"1) Identificar instancias Budibase 2) Actualizar a 4.0.0+ 3) Validar filtros MongoDB 4) Revisar cuentas globales","epss":null,"iocs":[],"sigma":"detection: prototype pollution in Budibase","playbook":{"triage":["Verificar version de Budibase","Revisar filtros MongoDB"],"contencion":["Actualizar a 4.0.0+","Aislar instancia"],"verificacion":["Confirmar version","Validar integridad de datos"]}}, {"id":"CVE-2026-137499","producto":"Budibase","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1078","nombre":"Valid Accounts"},"explotacion":"sin explotacion confirmada","parche":"Actualizar Budibase a 4.0.0+","runbook":"1) Identificar instancias Budibase 2) Actualizar a 4.0.0+ 3) Revisar cuentas globales sin contrasena 4) Forzar rotacion de credenciales","epss":null,"iocs":[],"sigma":"detection: auth bypass in Budibase global accounts","playbook":{"triage":["Verificar version de Budibase","Revisar cuentas globales"],"contencion":["Actualizar a 4.0.0+","Forzar rotacion de credenciales"],"verificacion":["Confirmar version","Validar autenticacion"]}}],"otras_amenazas":[]}
```


# 📋 Informe CTI Diario - 2026-11-25

**Fecha del informe:** 26/11/2026  **Cobertura:** novedades del miércoles 25 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) 🟠 Alta (7-8.9) 🟡 Media (4-6.9) 🟢 Baja (<4).

> ⚡ **Resumen en 30 segundos:** CISA añadió 10 CVE al KEV el 25/11: 5 en Mitel MiCollab (uno CVSS 10.0), además de Vates Xen Orchestra (9.8, explotado desde IP 91.211.251.112), HPE Juniper Session Smart (9.8, explotado), y 3 Danfoss AK-SM 800A (8.5-9.9, vinculados a ataques a supermercados europeos). Plazos FCEB: 28/11 (mayoría) y 09/12 (CVE-2026-133311 y CVE-2025-41450). Además CISA alertó de CVE-2026-129713 en Danfoss (CVSS 9.8/9.3, sin parche).

## 1. Turno de vigilancia — qué atender primero

### Top 5 atacar hoy

| # | CVE | Severidad | Acción | Tiempo est. |
|---|---|---|---|---|
| 1 | CVE-2026-133323 (Mitel MiCollab) | 🔴 10.0 | Actualizar MiCollab a 10.3.0.18 + parche. | 1-2 h |
| 2 | CVE-2026-133469 (Vates Xen Orchestra) | 🔴 9.8 | Parchear Xen Orchestra. Bloquear IP 91.211.251.112 y dominio .onion. | 1 h |
| 3 | CVE-2026-103406 (HPE Juniper Session Smart) | 🔴 9.8 | Actualizar Session Smart Conductor/Router. | 1-2 h |
| 4 | CVE-2026-133303 (Mitel MiCollab) | 🔴 9.0 | Actualizar MiCollab a 10.3.0.18 + parche. | 1-2 h |
| 5 | CVE-2026-129713 (Danfoss AK-SM 8xxA) | 🔴 9.8/9.3 | Sin parche: VPN/Strict mode, aislar red OT. | 2-4 h |

### Prioridades

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 - Mitel MiCollab (5 CVEs) | Actualizar a 10.3.0.18 + parche; restringir acceso externo al componente AWV; revisar logs de subida de archivos y comandos. |
| 2 | 🔴 P1 - Xen Orchestra (CVE-2026-133469) | Parchear; bloquear IOC 91.211.251.112:8001 y ustar3rqxgnskzgnwkgpofgtvxevm6cx3lxdbwt4mndtveqyaohid.onion:3333; revisar tokens de autenticación vacíos. |
| 3 | 🔴 P1 - HPE Juniper (CVE-2026-103406) | Actualizar Session Smart; revisar salt minion IDs sospechosos. |
| 4 | 🟠 P2 - Danfoss AK-SM 800A (3 CVEs) | Parchear firmware; vinculado a ataques a supermercados europeos (Shahid Kaveh/IRGC). |
| 5 | 🟠 P2 - Danfoss AK-SM 8xxA (CVE-2026-129713, sin parche) | Aplicar mitigaciones temporales (VPN, Strict mode, aislamiento). |
| 6 | 🟡 General | Vigilar NVD/KEV; verificar Shadowserver para explotación en Internet. |

> ℹ️ CISA añadió 10 CVE al KEV el 25/11. Mitel publicó parche el 16/11; Vates y Juniper confirmaron explotación activa.

## 2. Recomendaciones SOC L1

1. 🔴 **Mitel MiCollab (CVE-2026-133275/133303/133311/133323/133324):** actualizar a 10.3.0.18 + parche; restringir acceso externo al componente AWV; revisar logs de subida de archivos arbitrarios, inyección XML y bypass de autenticación.
2. 🔴 **Xen Orchestra (CVE-2026-133469):** parchear; bloquear IOCs (91.211.251.112, dominio .onion); revisar tokens de autenticación con objeto JSON vacío; buscar acciones administrativas no autorizadas.
3. 🔴 **HPE Juniper (CVE-2026-103406):** actualizar Session Smart; revisar salt minion IDs con caracteres especiales; buscar ejecución de comandos como root.
4. 🟠 **Danfoss AK-SM 800A (CVE-2023-25914/25915, CVE-2025-41450):** actualizar firmware; revisar logs de acceso al interfaz XML y generación de informes web.
5. 🟠 **Danfoss AK-SM 8xxA (CVE-2026-129713, sin parche):** aplicar VPN/Strict mode/aislamiento; monitorear peticiones HTTP POST a handlers CGI.

## 3. Recomendaciones SOC L2

1. 🟠 **Hardening:** segmentar redes OT/ICS de redes de negocio; aplicar microsegmentación; restringir acceso externo a componentes de conferencia (AWV).
2. 🟠 **Threat hunting:** buscar en logs conexiones desde IPs de amenaza (91.211.251.112); buscar tokens vacíos en autenticación; buscar ejecución de comandos en MiCollab y Xen Orchestra.
3. 🟠 **Tuning de detecciones:** alertar ante nuevas adiciones KEV; detectar inyección XML y command injection en MiCollab; detectar bypass de autenticación en Xen Orchestra.
4. 🟠 **Mejoras de proceso:** documentar estado de remediación por CVE; usar fechas KEV (BOD 26-04) como SLA; priorizar dispositivos OT sin parche.

## 4. Resumen ejecutivo

- CISA añadió 10 CVE al KEV el 25/11: 5 Mitel MiCollab, 1 Vates Xen Orchestra, 1 HPE Juniper, 3 Danfoss AK-SM 800A.
- Xen Orchestra (CVE-2026-133469, CVSS 9.8) explotado activamente desde IP 91.211.251.112 y dominio .onion.
- HPE Juniper (CVE-2026-103406, CVSS 9.8) con al menos un caso de explotación reportado.
- Danfoss AK-SM 800A vinculado a ataques a supermercados europeos atribuidos a Shahid Kaveh (IRGC).
- CISA alertó de CVE-2026-129713 en Danfoss AK-SM 8xxA (CVSS 9.8/9.3) sin parche disponible.

## 5. Hallazgos

### En simple
CISA añadió 10 vulnerabilidades al catálogo KEV el 25/11. Cinco afectan Mitel MiCollab (componente AWV), incluyendo una de severidad máxima (CVSS 10.0) que permite inyección de comandos. Vates Xen Orchestra tiene un bypass de autenticación (CVSS 9.8) que ya fue explotado desde una IP específica. HPE Juniper Session Smart tiene inyección de comandos OS (CVSS 9.8) también explotada. Tres fallos en Danfoss AK-SM 800A (controladores de refrigeración) están vinculados a ataques a supermercados europeos. Además, CISA alertó de una falla sin parche en Danfoss AK-SM 8xxA.

### Detalle técnico

**CVE-2026-133275** — Mitel MiCollab (AWV): subida arbitraria de archivos, CVSS 5.3. Versiones ≤10.3. Parche: 10.3.0.18.
**CVE-2026-133303** — Mitel MiCollab (AWV): inyección XML → ejecución de comandos, CVSS 9.0. Versiones ≤10.3. Parche: 10.3.0.18.
**CVE-2026-133311** — Mitel MiCollab (AWV): acceso no autorizado / fuga de info, CVSS 5.9. Versiones ≤10.3. Parche: 10.3.0.18.
**CVE-2026-133323** — Mitel MiCollab (AWV): inyección de comandos OS, CVSS 10.0. Versiones ≤10.3. Parche: 10.3.0.18.
**CVE-2026-133324** — Mitel MiCollab (AWV): bypass de autenticación, CVSS 5.3. Versiones ≤10.3. Parche: 10.3.0.18.
**CVE-2026-133469** — Vates Xen Orchestra: bypass de autenticación mediante token con objeto JSON vacío, CVSS 9.8. Explotado desde 91.211.251.112:8001 y dominio .onion:3333.
**CVE-2026-103406** — HPE Juniper Session Smart Conductor/Router: inyección de comandos OS via salt minion ID, CVSS 9.8. Explotado (reportado al SIRT).
**CVE-2023-25914** — Danfoss AK-SM 800A: path traversal via interfaz XML, CVSS 8.8.
**CVE-2023-25915** — Danfoss AK-SM 800A: validación de entrada incorrecta → ejecución de comandos, CVSS 9.9.
**CVE-2025-41450** — Danfoss AK-SM 800A: autenticación incorrecta → fuga de info (IPs, usuarios, nombres de tienda), CVSS 8.5.
**CVE-2026-129713** — Danfoss AK-SM 8xxA: buffer overflow en handler CGI no autenticado, CVSS 9.8/9.3. Sin parche (previsto en 5.5.x). Mitigaciones: VPN, Strict mode, aislamiento.

### Tabla comparativa

| CVE | Producto | CVSS | Explotación | Plazo FCEB |
|---|---|---|---|---|
| CVE-2026-133275 | Mitel MiCollab | 5.3 | KEV | 28/11 |
| CVE-2026-133303 | Mitel MiCollab | 9.0 | KEV | 28/11 |
| CVE-2026-133311 | Mitel MiCollab | 5.9 | KEV | 09/12 |
| CVE-2026-133323 | Mitel MiCollab | 10.0 | KEV | 28/11 |
| CVE-2026-133324 | Mitel MiCollab | 5.3 | KEV | 28/11 |
| CVE-2026-133469 | Vates Xen Orchestra | 9.8 | KEV + activa | 28/11 |
| CVE-2026-103406 | HPE Juniper Session Smart | 9.8 | KEV + activa | 28/11 |
| CVE-2023-25914 | Danfoss AK-SM 800A | 8.8 | KEV | 28/11 |
| CVE-2023-25915 | Danfoss AK-SM 800A | 9.9 | KEV | 28/11 |
| CVE-2025-41450 | Danfoss AK-SM 800A | 8.5 | KEV | 09/12 |
| CVE-2026-129713 | Danfoss AK-SM 8xxA | 9.8/9.3 | Sin parche | N/A |

## 6. Contexto de seguimiento

- CVE-2026-134624 (N-able MailAssure): sin novedades; parche 20261121.1943 disponible.
- CVEs deadline 23/11 (vCenter, Roller, Expressway, Synology, NGINX, SAP, SonicWall): verificar cierre.

## 7. Cobertura y limitaciones

Fuentes: CISA KEV/alertas, NIST NVD, MITRE ATT&CK, The Hacker News, fabricantes oficiales (Mitel, Vates, Juniper, Danfoss). No se encontraron IOCs adicionales para Mitel/HPE/Juniper más allá de los documentados. EPSS no disponible en fuentes consultadas.

## META DEL PANEL (no se muestra en la tarjeta)
```json
{"fecha":"2026-11-25","cves":[{"id":"CVE-2026-133275","producto":"Mitel MiCollab","cvss":5.3,"severidad":"Media","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a 10.3.0.18 + parche","runbook":"Actualizar MiCollab; restringir acceso externo AWV; revisar logs de subida de archivos","epss":null,"iocs":[],"sigma":"detection: mitel_micollab_arbitrary_file_upload","playbook":{"triage":["Verificar versión MiCollab","Revisar logs AWV"],"contencion":["Restringir acceso externo AWV","Aplicar parche"],"verificacion":["Confirmar versión 10.3.0.18","Validar logs limpios"]}}, {"id":"CVE-2026-133303","producto":"Mitel MiCollab","cvss":9.0,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a 10.3.0.18 + parche","runbook":"Actualizar MiCollab; revisar inyección XML en AWV","epss":null,"iocs":[],"sigma":"detection: mitel_micollab_xml_injection","playbook":{"triage":["Verificar versión","Revisar logs XML"],"contencion":["Aplicar parche","Restringir AWV"],"verificacion":["Confirmar parche","Validar sin comandos ejecutados"]}}, {"id":"CVE-2026-133311","producto":"Mitel MiCollab","cvss":5.9,"severidad":"Media","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a 10.3.0.18 + parche","runbook":"Actualizar MiCollab; revisar accesos no autorizados","epss":null,"iocs":[],"sigma":"detection: mitel_micollab_unauth_access","playbook":{"triage":["Verificar versión","Revisar logs de acceso"],"contencion":["Aplicar parche"],"verificacion":["Confirmar versión","Validar sin fugas"]}}, {"id":"CVE-2026-133323","producto":"Mitel MiCollab","cvss":10.0,"severidad":"Critica","mitre":{"id":"T1059","nombre":"Command and Scripting Interpreter"},"explotacion":"KEV","parche":"Actualizar a 10.3.0.18 + parche","runbook":"Actualizar MiCollab; buscar ejecución de comandos en AWV","epss":null,"iocs":[],"sigma":"detection: mitel_micollab_command_injection","playbook":{"triage":["Verificar versión","Buscar comandos ejecutados"],"contencion":["Aplicar parche","Aislar AWV"],"verificacion":["Confirmar versión","Validar sin RCE"]}}, {"id":"CVE-2026-133324","producto":"Mitel MiCollab","cvss":5.3,"severidad":"Media","mitre":{"id":"T1078","nombre":"Valid Accounts"},"explotacion":"KEV","parche":"Actualizar a 10.3.0.18 + parche","runbook":"Actualizar MiCollab; revisar bypass de autenticación","epss":null,"iocs":[],"sigma":"detection: mitel_micollab_auth_bypass","playbook":{"triage":["Verificar versión","Revisar logs auth"],"contencion":["Aplicar parche"],"verificacion":["Confirmar versión","Validar auth"]}}, {"id":"CVE-2026-133469","producto":"Vates Xen Orchestra","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1078","nombre":"Valid Accounts"},"explotacion":"KEV","parche":"Parchear Xen Orchestra","runbook":"Parchear; bloquear IP 91.211.251.112; revisar tokens vacíos","epss":null,"iocs":["91.211.251.112:8001","ustar3rqxgnskzgnwkgpofgtvxevm6cx3lxdbwt4mndtveqyaohid.onion:3333"],"sigma":"detection: xen_orchestra_empty_json_token_bypass","playbook":{"triage":["Revisar tokens vacíos","Buscar acciones admin"],"contencion":["Bloquear IPs","Parchear"],"verificacion":["Confirmar parche","Validar sin bypass"]}}, {"id":"CVE-2026-103406","producto":"HPE Juniper Session Smart","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1059","nombre":"Command and Scripting Interpreter"},"explotacion":"KEV","parche":"Actualizar Session Smart","runbook":"Actualizar; revisar salt minion IDs sospechosos","epss":null,"iocs":[],"sigma":"detection: juniper_session_smart_salt_minion_injection","playbook":{"triage":["Revisar salt minion IDs","Buscar comandos root"],"contencion":["Actualizar firmware"],"verificacion":["Confirmar versión","Validar sin RCE"]}}, {"id":"CVE-2023-25914","producto":"Danfoss AK-SM 800A","cvss":8.8,"severidad":"Alta","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar firmware","runbook":"Parchear firmware; revisar acceso XML","epss":null,"iocs":[],"sigma":"detection: danfoss_aksm800a_path_traversal","playbook":{"triage":["Verificar firmware","Revisar logs XML"],"contencion":["Parchear"],"verificacion":["Confirmar firmware","Validar sin traversal"]}}, {"id":"CVE-2023-25915","producto":"Danfoss AK-SM 800A","cvss":9.9,"severidad":"Critica","mitre":{"id":"T1059","nombre":"Command and Scripting Interpreter"},"explotacion":"KEV","parche":"Actualizar firmware","runbook":"Parchear firmware; buscar comandos ejecutados","epss":null,"iocs":[],"sigma":"detection: danfoss_aksm800a_command_injection","playbook":{"triage":["Verificar firmware","Buscar comandos"],"contencion":["Parchear"],"verificacion":["Confirmar firmware","Validar sin RCE"]}}, {"id":"CVE-2025-41450","producto":"Danfoss AK-SM 800A","cvss":8.5,"severidad":"Alta","mitre":{"id":"T1078","nombre":"Valid Accounts"},"explotacion":"KEV","parche":"Actualizar firmware","runbook":"Parchear firmware; revisar informes web generados","epss":null,"iocs":[],"sigma":"detection: danfoss_aksm800a_info_disclosure","playbook":{"triage":["Verificar firmware","Revisar informes web"],"contencion":["Parchear"],"verificacion":["Confirmar firmware","Validar sin fuga"]}}, {"id":"CVE-2026-129713","producto":"Danfoss AK-SM 8xxA","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"sin explotacion confirmada","parche":"Sin parche (previsto 5.5.x); mitigaciones temporales","runbook":"Aplicar VPN/Strict mode/aislamiento; monitorear CGI POST","epss":null,"iocs":[],"sigma":"detection: danfoss_aksm8xxa_cgi_buffer_overflow","playbook":{"triage":["Verificar versión ≤5.4.0","Monitorear CGI POST"],"contencion":["VPN/Strict mode","Aislar red OT"],"verificacion":["Confirmar mitigaciones","Validar sin explotación"]}}],"otras_amenazas":[{"nombre":"Shahid Kaveh (Cyber Aveng3rs)","tipo":"APT","resumen":"Unidad cibernética iraní vinculada a ataques a supermercados europeos usando Danfoss AK-SM 800A."}]}

---

# 📋 Informe CTI Diario - 2026-11-24

**Fecha del informe:** 25/11/2026  **Cobertura:** novedades del martes 24 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) 🟠 Alta (7-8.9) 🟡 Media (4-6.9) 🟢 Baja (<4).

> ⚡ **Resumen en 30 segundos:** CISA añadió 3 CVE al KEV el 24/11: CVE-2026-131914 (Idunno AntiCsrf deserialización, CVSS 9.8), CVE-2026-132165 (Drupal Core RCE, CVSS 9.8, Fail2Install) y CVE-2026-132224 (Optimizely Web Experimentation, CVSS 8.1, usado en skimming de tarjetas en CNN/BBC/Zoom).

## 1. Turno de vigilancia — qué atender primero

### Top 5 atacar hoy

| # | CVE | Severidad | Acción | Tiempo est. |
|---|---|---|---|---|
| 1 | CVE-2026-132165 (Drupal Core) | 🔴 9.8 | Actualizar Drupal; deadline 27/11. | 1-2 h |
| 2 | CVE-2026-131914 (Idunno AntiCsrf) | 🔴 9.8 | Parchear/actualizar componente AntiCsrf. | 1 h |
| 3 | CVE-2026-132224 (Optimizely) | 🟠 8.1 | Auditar scripts Optimizely en sitios web; verificar integridad. | 2-4 h |
| 4 | CVE-2026-134624 (N-able MailAssure) | 🔴 9.3 | Actualizar a 20261121.1943; rotar credenciales. | 30 min-1 h |
| 5 | CVE-2026-59310 (VMware vCenter) | 🔴 9.8 | Parchear vCenter; deadline vencida. | 1-2 h |

### Prioridades

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 - Drupal Core (CVE-2026-132165) | Actualizar Drupal Core; revisar instaladores; buscar RCE (Fail2Install). Deadline 27/11. |
| 2 | 🔴 P1 - Idunno AntiCsrf (CVE-2026-131914) | Parchear componente AntiCsrf; buscar deserialización de datos no confiables. |
| 3 | 🟠 P2 - Optimizely (CVE-2026-132224) | Auditar scripts Optimizely en sitios; verificar integridad de CDN; buscar skimmers de tarjetas. |
| 4 | 🟠 P2 - N-able MailAssure (CVE-2026-134624) | Actualizar a 20261121.1943; rotar credenciales/API tokens. |
| 5 | 🟡 General | Vigilar NVD/KEV; verificar Shadowserver. |

> ℹ️ CISA añadió 3 CVE al KEV el 24/11. Optimizely usado en ataque de skimming a CNN, BBC y Zoom.

## 2. Recomendaciones SOC L1

1. 🔴 **Drupal Core (CVE-2026-132165):** actualizar Drupal Core; revisar instaladores expuestos; buscar RCE (Fail2Install / Too Many Connections). Deadline 27/11.
2. 🔴 **Idunno AntiCsrf (CVE-2026-131914):** parchear componente; buscar deserialización de datos no confiables en logs.
3. 🟠 **Optimizely (CVE-2026-132224):** auditar scripts Optimizely en sitios web; verificar integridad de CDN; buscar skimmers de tarjetas y kits de exploit iOS.
4. 🟠 **N-able MailAssure (CVE-2026-134624):** actualizar a 20261121.1943; rotar credenciales/API tokens.

## 3. Recomendaciones SOC L2

1. 🟠 **Hardening:** segmentar CMS (Drupal) de red interna; aplicar WAF con reglas anti-deserialización; restringir acceso a instaladores.
2. 🟠 **Threat hunting:** buscar en logs deserialización en AntiCsrf; buscar skimmers en scripts Optimizely; buscar RCE en Drupal (Fail2Install).
3. 🟠 **Tuning de detecciones:** alertar ante nuevas adiciones KEV; detectar manipulación de scripts de terceros (Optimizely); detectar CVE-2026-132165 (Drupal).
4. 🟠 **Mejoras de proceso:** documentar estado de remediación por CVE; usar fechas KEV como SLA.

## 4. Resumen ejecutivo

- CISA añadió 3 CVE al KEV el 24/11: Idunno AntiCsrf (9.8), Drupal Core RCE (9.8), Optimizely (8.1).
- Drupal Core RCE (Fail2Install) con deadline 27/11; explotado en la calle.
- Optimizely usado en ataque de skimming de tarjetas a visitantes de CNN, BBC y Zoom.
- Idunno AntiCsrf: deserialización de datos no confiables, sin detalles de explotación.

## 5. Hallazgos

### En simple
El 24/11 CISA añadió 3 vulnerabilidades al KEV. Drupal Core tiene un RCE (Fail2Install) que permite a un atacante tomar control de sitios web sin autenticación. Idunno AntiCsrf tiene una falla de deserialización que permite ejecución de código. Optimizely Web Experimentation fue explotado para inyectar un skimmer de tarjetas en sitios de alto tráfico (CNN, BBC, Zoom).

### Detalle técnico

**CVE-2026-131914** — Idunno AntiCsrf: deserialización de datos no confiables, CVSS 9.8. Sin detalles de explotación pública adicionales.
**CVE-2026-132165** — Drupal Core: RCE (Fail2Install / Too Many Connections), CVSS 9.8. Explotado en la calle; deadline 27/11. Afecta instaladores de Drupal.
**CVE-2026-132224** — Optimizely Web Experimentation: control externo de datos de estado crítico, CVSS 8.1. Usado en skimming de tarjetas y kits de exploit iOS en CNN, BBC y Zoom.

### Tabla comparativa

| CVE | Producto | CVSS | Explotación | Plazo FCEB |
|---|---|---|---|---|
| CVE-2026-131914 | Idunno AntiCsrf | 9.8 | KEV | 27/11 |
| CVE-2026-132165 | Drupal Core | 9.8 | KEV + activa | 27/11 |
| CVE-2026-132224 | Optimizely Web Experimentation | 8.1 | KEV + activa | 27/11 |

## 6. Contexto de seguimiento

- CVE-2026-134624 (N-able MailAssure): sin novedades.
- CVEs deadline 23/11 (vCenter, Roller, Expressway, Synology, NGINX, SAP, SonicWall): verificar cierre.

## 7. Cobertura y limitaciones

Fuentes: CISA KEV/alertas, NIST NVD, The Hacker News, fabricantes oficiales. No se encontraron IOCs específicos para Idunno AntiCsrf. EPSS no disponible en fuentes consultadas.

## META DEL PANEL (no se muestra en la tarjeta)
```json
{"fecha":"2026-11-24","cves":[{"id":"CVE-2026-131914","producto":"Idunno AntiCsrf","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Parchear componente AntiCsrf","runbook":"Parchear AntiCsrf; buscar deserialización en logs","epss":null,"iocs":[],"sigma":"detection: idunno_anticsrf_deserialization","playbook":{"triage":["Verificar componente","Revisar logs deserialización"],"contencion":["Parchear"],"verificacion":["Confirmar parche","Validar sin RCE"]}}, {"id":"CVE-2026-132165","producto":"Drupal Core","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar Drupal Core","runbook":"Actualizar Drupal; revisar instaladores; buscar RCE","epss":null,"iocs":[],"sigma":"detection: drupal_fail2install_rce","playbook":{"triage":["Verificar versión Drupal","Revisar instaladores"],"contencion":["Actualizar Core","Restringir instaladores"],"verificacion":["Confirmar versión","Validar sin RCE"]}}, {"id":"CVE-2026-132224","producto":"Optimizely Web Experimentation","cvss":8.1,"severidad":"Alta","mitre":{"id":"T1185","nombre":"Browser Session Hijacking"},"explotacion":"KEV","parche":"Auditar scripts Optimizely; verificar integridad CDN","runbook":"Auditar scripts; verificar integridad CDN; buscar skimmers","epss":null,"iocs":[],"sigma":"detection: optimizely_script_tampering","playbook":{"triage":["Auditar scripts Optimizely","Verificar CDN"],"contencion":["Eliminar scripts maliciosos","Bloquear CDN comprometido"],"verificacion":["Confirmar integridad","Validar sin skimmers"]}}],"otras_amenazas":[{"nombre":"Skimming CNN/BBC/Zoom","tipo":"campana","resumen":"Ataque de skimming de tarjetas usando scripts Optimizely comprometidos en sitios de alto tráfico."}]}
PYEOF
echo done

# 📡 Informe CTI Diario - 2026-11-23

**Fecha del informe:** 24/11/2026  **Cobertura:** novedades del lunes 23 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) · 🟠 Alta (7-8.9) · 🟡 Media (4-6.9) · 🟢 Baja (<4).

> ⚡ **Resumen en 30 segundos:** CISA añadió 1 CVE al KEV el 23/11: CVE-2026-134624 (N-able MailAssure/SpamExperts SSRF, CVSS 9.3), descubierto durante un breach activo en la nube de N-able. Parche disponible (versión 20261121.1943). MSPs y usuarios de Mail Assure deben actualizar ya y rotar credenciales.

## 1. Turno de vigilancia — qué atender primero

### Top 5 atacar hoy

| # | CVE | Severidad | Acción | Tiempo est. |
|---|---|---|---|---|
| 1 | CVE-2026-134624 (N-able MailAssure) | 🔴 9.3 | Actualizar a 20261121.1943. Rotar credenciales/API tokens. | 30 min-1 h |
| 2 | CVE-2026-59310 (VMware vCenter) | 🔴 9.8 | Parchear vCenter. Deadline 23/11 vencida. | 1-2 h |
| 3 | CVE-2026-129448 (Apache Roller) | 🔴 9.8 | Actualizar a 6.1.6. Deadline 23/11 vencida. | 30 min-1 h |
| 4 | CVE-2026-76466 (Cisco Expressway) | 🔴 9.8 | Actualizar a X15.5.20. Deadline 23/11 vencida. | 1-2 h |
| 5 | CVE-2024-10441 (Synology DSM) | 🔴 9.8 | Parchear DSM. Ransomware C3RB3R. Deadline 23/11 vencida. | 1-2 h |

### Prioridades

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 - CVE-2026-134624 (N-able MailAssure) | Actualizar Mail Assure/SpamExperts a 20261121.1943; rotar credenciales y tokens API/OAuth; revisar logs de LDAP sync connection check. |
| 2 | 🟠 P2 - CVEs deadline 23/11 | Verificar cierre de vCenter, Roller, Expressway, Synology DSM, NGINX, SAP NetWeaver, SonicWall. |
| 3 | 🟡 General | Vigilar NVD/KEV; verificar Shadowserver para explotación en Internet. |

> ℹ️ CISA añadió 1 CVE al KEV el 23/11. N-able reveló el parche tras un breach activo en su nube.

## 2. Recomendaciones SOC L1

1. 🔴 **Prioridad 1 - N-able MailAssure (CVE-2026-134624):** actualizar a 20261121.1943; rotar todas las credenciales, API tokens y OAuth tokens; revisar logs de conexión LDAP sync; buscar conexiones internas anómalas desde el servidor Mail Assure.
2. 🟠 **Prioridad 2 - Deadline 23/11:** verificar estado de parcheo de vCenter, Roller, Expressway, Synology DSM, NGINX, SAP NetWeaver, SonicWall.
3. 🟡 **General:** revisar logs de sistemas afectados; buscar IOCs conocidos (webshells, hashes, IPs, rutas de webshell).

## 3. Recomendaciones SOC L2

1. 🟠 **Hardening:** segmentar el servidor Mail Assure de la red interna; aplicar microsegmentación east-west; restringir destinos de conexión SSRF.
2. 🟠 **Threat hunting:** buscar en logs conexiones internas anómalas desde Mail Assure hacia puertos no estándar; buscar enumeración de servicios internos.
3. 🟡 **Tuning de detecciones:** alertar ante nuevas adiciones KEV; mantener reglas Sigma/YARA existentes; detectar SSRF en conexión LDAP sync.
4. 🟡 **Mejoras de proceso:** documentar estado de remediación por CVE; usar fechas KEV (BOD 26-04) como SLA interno.

## 4. Resumen ejecutivo

- CISA añadió 1 CVE al KEV el 23/11: CVE-2026-134624 (N-able MailAssure SSRF, CVSS 9.3, explotado en la calle).
- La vulnerabilidad fue descubierta durante un breach activo en la nube de N-able (Mail Assure y SpamExperts Hosted Cloud).
- Parche disponible (versión 20261121.1943); N-able rotó credenciales y revocó tokens.
- La prioridad sigue siendo cerrar los CVEs con deadline 23/11 (vCenter, Roller, Expressway, Synology, NGINX, SAP, SonicWall).

## 5. Hallazgos

### En simple
CVE-2026-134624 es una falla SSRF (falsificación de solicitud del lado del servidor) en N-able Mail Assure/SpamExperts, descubierta durante un breach activo en la nube de N-able. Un atacante autenticado con privilegios de API puede manipular la comprobación de conexión de sincronización LDAP para que el servidor Mail Assure se conecte a hosts internos arbitrarios, alcanzando servicios internos, almacenes de credenciales y endpoints de API, lo que permite escalación de privilegios y robo de credenciales. N-able parcheó la falla (versión 20261121.1943), rotó credenciales y revocó tokens; CISA la incluyó en KEV con deadline 26/11.

### Detalle técnico
- **CVE:** CVE-2026-134624
- **CVSS:** 9.3 (Crítica, CVSSv3) — NVD reporta 8.7 en algunas fuentes; N-able reporta 9.3.
- **CWE:** CWE-918 (Server-Side Request Forgery)
- **Producto:** N-able Mail Assure (SpamExperts) Hosted Cloud < 20261121.1943
- **Técnica MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1021 (Remote Services) para el movimiento lateral interno mediante SSRF.
- **Explotación:** KEV (añadido 23/11/2026), explotado en la calle. Breach activo en la nube de N-able (Mail Assure y SpamExperts Hosted Cloud). Atacante autenticado con privilegios de API.
- **Impacto:** acceso a hosts y servicios internos de la plataforma; escalación de privilegios mediante targeting de almacenes de credenciales y endpoints de servicio; robo de credenciales, creación de admins, ejecución de código como root en el servidor Mail Assure.
- **Mitigaciones/Parches:** actualizar a Mail Assure 20261121.1943; rotar credenciales, API tokens y OAuth tokens; aplicar segmentación east-west y microsegmentación; restringir destinos de conexión LDAP sync.
- **EPSS:** no disponible en fuentes consultadas.
- **IOCs:** conexiones internas anómalas desde el servidor Mail Assure hacia puertos no estándar; enumeración de servicios internos; actividad de LDAP sync inusual.
- **Sigma/YARA:** `title: N-able MailAssure SSRF LDAP Sync (CVE-2026-134624) | description: Detects anomalous internal connections from Mail Assure server indicative of SSRF exploitation | logsource: network | detection: selection: src_host: 'mailassure' AND dst_port: not in (389, 636, 443, 80) | condition: selection`
- **Playbook:**
  - Triage: verificar versión de Mail Assure desplegada; revisar logs de LDAP sync connection check; buscar conexiones internas anómalas desde el servidor.
  - Contención: actualizar a 20261121.1943; rotar credenciales y tokens; aplicar segmentación east-west; restringir destinos de conexión.
  - Verificación: confirmar versión parcheada; validar ausencia de conexiones internas anómalas post-parche; auditar accesos a servicios internos.

## 6. Contexto de seguimiento
- CVE-2026-134624 (N-able MailAssure): nuevo en KEV el 23/11, deadline 26/11.
- CVE-2026-124194 (Outlook): sin cambios, deadline 24/11.
- CVE-2026-59310 (vCenter): sin cambios, deadline 23/11 vencida.
- CVE-2026-129448 (Roller): sin cambios, deadline 23/11 vencida.
- CVE-2026-76466 (Expressway): sin cambios, deadline 23/11 vencida.
- CVE-2024-10441 (Synology DSM): sin cambios, deadline 23/11 vencida.
- CVE-2026-123007 (NGINX): sin cambios.
- CVE-2025-42884 (SAP NetWeaver): sin cambios, deadline 23/11 vencida.
- CVE-2026-19868 (SonicWall): sin cambios.

## 7. Cobertura y limitaciones
- Fuentes consultadas: CISA KEV, NVD, securityonline.info, SecurityWeek, The Hacker News, Aviatrix. 1 nueva adición KEV el 23/11.
- EPSS no disponible para CVE-2026-134624 en las fuentes consultadas.
- IOCs no publicados por N-able.
- Posibles CVEs no incluidos si no aparecieron en fuentes priorizadas.

```json
{"fecha":"2026-11-23","cves":[{"id":"CVE-2026-134624","producto":"N-able Mail Assure (SpamExperts) Hosted Cloud","cvss":9.3,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a Mail Assure 20261121.1943; rotar credenciales y tokens API/OAuth","runbook":"1) Verificar version de Mail Assure desplegada. 2) Actualizar a 20261121.1943. 3) Rotar credenciales y tokens. 4) Revisar logs de LDAP sync connection check.","epss":null,"iocs":["conexiones internas anomalas desde Mail Assure","enumeracion de servicios internos","actividad de LDAP sync inusual"],"sigma":"Regla Sigma: deteccion de conexiones internas anomalas desde servidor Mail Assure indicativas de SSRF (puertos no estandar).","playbook":{"triage":["Verificar version de Mail Assure desplegada.","Revisar logs de LDAP sync connection check.","Buscar conexiones internas anomalas desde el servidor."],"contencion":["Actualizar a 20261121.1943.","Rotar credenciales y tokens.","Aplicar segmentacion east-west.","Restringir destinos de conexion."],"verificacion":["Confirmar version parcheada.","Validar ausencia de conexiones internas anomalas post-parche.","Auditar accesos a servicios internos."]}}],"otras_amenazas":[]}
```

---

# 📡 Informe CTI Diario - 2026-11-22

**Fecha del informe:** 23/11/2026  **Cobertura:** novedades del domingo 22 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) · 🟠 Alta (7-8.9) · 🟡 Media (4-6.9) · 🟢 Baja (<4).

> ⚡ **Resumen en 30 segundos:** CISA no añadió nuevos CVEs al KEV el 22/11. Se reporta por primera vez CVE-2026-124194 (Microsoft Outlook spoofing, CVSS 6.5, KEV, deadline federal 24/11) con explotación activa y fuga de hash NTLMv2. La prioridad sigue siendo cerrar los CVEs con deadline 23/11 (vCenter, Roller, Expressway, Synology, NGINX, SAP, SonicWall).

## 1. Turno de vigilancia — qué atender primero

### Top 5 atacar hoy

| # | CVE | Severidad | Acción | Tiempo est. |
|---|---|---|---|---|
| 1 | CVE-2026-59310 (VMware vCenter) | 🔴 9.8 | Parchear vCenter. Deadline 23/11. | 1-2 h |
| 2 | CVE-2026-129448 (Apache Roller) | 🔴 9.8 | Actualizar a 6.1.6. Deadline 23/11. | 30 min-1 h |
| 3 | CVE-2026-76466 (Cisco Expressway) | 🔴 9.8 | Actualizar a X15.5.20. Deadline 23/11. | 1-2 h |
| 4 | CVE-2024-10441 (Synology DSM) | 🔴 9.8 | Parchear DSM. Ransomware C3RB3R activo. Deadline 23/11. | 1-2 h |
| 5 | CVE-2026-123007 (NGINX) | 🔴 9.8 | Actualizar NGINX. PoC público. | 30 min-1 h |

### Prioridades

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 - CVE-2026-124194 (Outlook) | Aplicar November 2026 Office updates. Deadline 24/11. Bloquear SMB/NTLM saliente. |
| 2 | 🟠 P2 - CVEs deadline 23/11 | Cerrar vCenter, Roller, Expressway, Synology DSM, NGINX, SAP NetWeaver, SonicWall. |
| 3 | 🟡 General | Vigilar NVD/KEV; verificar Shadowserver para explotación en Internet. |

> ℹ️ Día sin nuevas adiciones al KEV. Se reporta CVE-2026-124194 por primera vez (KEV desde 10/11).

## 2. Recomendaciones SOC L1

1. 🔴 **Prioridad 1 - Outlook (CVE-2026-124194):** aplicar updates de Office; alertar a usuarios no abrir archivos de mensaje inesperados; bloquear SMB/NTLM saliente a Internet.
2. 🟠 **Prioridad 2 - Deadline 23/11:** verificar inventario y estado de parcheo de vCenter, Roller, Expressway, Synology DSM, NGINX, SAP NetWeaver, SonicWall.
3. 🟡 **General:** revisar logs de sistemas afectados; buscar IOCs conocidos (webshells, hashes, IPs).

## 3. Recomendaciones SOC L2

1. 🟠 **Hardening:** deshabilitar NTLM hacia Internet; restringir SMB saliente; segmentar vCenter.
2. 🟠 **Threat hunting:** buscar conexiones SMB/NTLM anómalas desde endpoints; buscar webshells en NGINX; buscar RPC anómalos en Expressway.
3. 🟡 **Tuning de detecciones:** alertar ante nuevas adiciones KEV; mantener reglas Sigma/YARA existentes.
4. 🟡 **Mejoras de proceso:** documentar estado de remediación por CVE antes de deadlines 23/11 y 24/11.

## 4. Resumen ejecutivo

- CISA no añadió nuevos CVEs al KEV el 22/11.
- Se reporta por primera vez CVE-2026-124194 (Outlook spoofing, CVSS 6.5, KEV, deadline 24/11) con explotación activa y fuga de hash NTLMv2.
- La prioridad sigue siendo cerrar los CVEs con deadline federal 23/11.
- Vigilancia continua de Shadowserver y VulnCheck KEV para nuevos indicadores.

## 5. Hallazgos

### En simple
CVE-2026-124194 es una falla en Microsoft Outlook que no avisa al usuario antes de acciones inseguras. Un atacante envía un archivo de mensaje manipulado que, al abrirse, hace que Outlook se conecte a un servidor controlado por el atacante sin mostrar la advertencia habitual, filtrando el hash NTLMv2 del usuario. Microsoft lo corrigió en el Patch Tuesday de noviembre de 2026; CISA lo incluyó en KEV con deadline 24/11.

### Detalle técnico
- **CVE:** CVE-2026-124194
- **CVSS:** 6.5 (Media, CVSSv3)
- **CWE:** CWE-356
- **Producto:** Microsoft 365 Apps for Enterprise; Office LTSC 2021 (< build 16.0.14334.21048); Office LTSC 2024 (< build 16.0.17932.21140)
- **Técnica MITRE ATT&CK:** T1557 (Adversary-in-the-Middle) / T1187 (Forced Authentication); MITRE no documenta una técnica única para este vector específico.
- **Explotación:** KEV (añadido 10/11/2026), explotado en la calle. Código de exploit maduro (exploit code maturity "High").
- **Impacto:** filtración de hash NTLMv2 del usuario, posible suplantación/autenticación como el usuario. Preview Pane no es vector de ataque.
- **Mitigaciones/Parches:** instalar updates de noviembre 2026 de Office; builds 16.0.14334.21048 / 16.0.17932.21140. Bloquear SMB y NTLM saliente a Internet. No abrir archivos de mensaje inesperados.
- **EPSS:** 1.9% (30 días)
- **IOCs:** No publicados por Microsoft.
- **Sigma/YARA:** Detección de conexiones SMB/NTLM salientes inusuales desde procesos de Outlook; regla genérica de forced authentication.
- **Playbook:**
  - Triage: verificar versión de Office instalada; confirmar si el usuario abrió archivos de mensaje inesperados; buscar conexiones SMB salientes.
  - Contención: aplicar parche; bloquear SMB/NTLM saliente; aislar endpoints sospechosos.
  - Verificación: confirmar build de Office; revisar logs de firewall/proxy; validar que no haya hashes NTLM filtrados.

## 6. Contexto de seguimiento
- CVE-2026-59310 (vCenter): sin cambios, deadline 23/11.
- CVE-2026-129448 (Roller): sin cambios, deadline 23/11.
- CVE-2026-76466 (Expressway): sin cambios, deadline 23/11.
- CVE-2024-10441 (Synology DSM): sin cambios, deadline 23/11.
- CVE-2026-123007 (NGINX): sin cambios.
- CVE-2025-42884 (SAP NetWeaver): sin cambios, deadline 23/11.
- CVE-2026-19868 (SonicWall): sin cambios.

## 7. Cobertura y limitaciones
- Fuentes consultadas: CISA KEV, NVD, securityonline.info, Microsoft MSRC. No se encontraron nuevas adiciones KEV el 22/11.
- Datos de EPSS e IOCs parciales (Microsoft no publicó IOCs para CVE-2026-124194).
- Posibles CVEs no incluidos si no aparecieron en fuentes priorizadas.

```json
{"fecha":"2026-11-22","cves":[{"id":"CVE-2026-124194","producto":"Microsoft Outlook / Microsoft 365 Apps","cvss":6.5,"severidad":"Media","mitre":{"id":"T1187","nombre":"Forced Authentication"},"explotacion":"KEV","parche":"Instalar updates de noviembre 2026 de Office (builds 16.0.14334.21048 / 16.0.17932.21140)","runbook":"1) Verificar versión de Office instalada. 2) Aplicar parche. 3) Bloquear SMB/NTLM saliente. 4) Revisar logs de conexiones SMB anómalas.","epss":0.019,"iocs":[],"sigma":"Detección de conexiones SMB/NTLM salientes inusuales desde procesos de Outlook","playbook":{"triage":["Verificar versión de Office","Confirmar si el usuario abrió archivos de mensaje inesperados","Buscar conexiones SMB salientes"],"contencion":["Aplicar parche","Bloquear SMB/NTLM saliente","Aislar endpoints sospechosos"],"verificacion":["Confirmar build de Office","Revisar logs de firewall/proxy","Validar que no haya hashes NTLM filtrados"]}}],"otras_amenazas":[]}
```

# 📡 Informe CTI Diario - 2026-11-21

**Fecha del informe:** 22/11/2026  **Cobertura:** novedades del sábado 21 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) · 🟠 Alta (7-8.9) · 🟡 Media (4-6.9) · 🟢 Baja (<4).

> ℹ️ **Resumen en 30 segundos:** CISA no añadió nuevos CVEs al KEV el 21/11. Sin hallazgos críticos nuevos ni PoC publicados ese día. La prioridad sigue siendo cerrar los CVEs del 18/11 con deadline federal 23/11 (Synology DSM, LiteLLM, Apache Axis, REDCap, NGINX) y los del 20/11 (vCenter, Roller, Expressway, SAP, SonicWall).

## 1. Turno de vigilancia — qué atender primero

### Top 5 atacar hoy

| # | CVE | Severidad | Acción | Tiempo est. |
|---|---|---|---|---|
| 1 | CVE-2026-59310 (VMware vCenter) | 🔴 9.8 | Parchear vCenter. Deadline 23/11. | 1-2 h |
| 2 | CVE-2026-129448 (Apache Roller) | 🔴 9.8 | Actualizar a 6.1.6. Deadline 23/11. | 30 min-1 h |
| 3 | CVE-2026-76466 (Cisco Expressway) | 🔴 9.8 | Actualizar a X15.5.20. Deadline 23/11. | 1-2 h |
| 4 | CVE-2024-10441 (Synology DSM) | 🔴 9.8 | Parchear DSM. Ransomware C3RB3R activo. Deadline 23/11. | 1-2 h |
| 5 | CVE-2026-123007 (NGINX) | 🔴 9.8 | Actualizar NGINX. PoC público. | 30 min-1 h |

### Prioridades

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 - Sin nuevos KEV el 21/11 | No hubo adiciones al KEV. Mantener foco en CVEs con deadline 23/11. |
| 2 | 🟠 P2 - vCenter / Roller / Expressway / Synology / NGINX | Cerrar parches antes del 23/11. Verificar inventario y estado de remediación. |
| 3 | 🟡 General | Vigilar NVD y KEV para nuevas adiciones; verificar Shadowserver para explotación en Internet de CVEs previos. |

> ℹ️ Día sin novedades críticas nuevas. Vigilancia continua de CVEs previamente informados.

## 2. Recomendaciones SOC L1

1. 🔴 **Prioridad 1 - Sin nuevos hallazgos:** no se requiere acción nueva. Mantener remediación de CVEs con deadline 23/11.
2. 🟠 **Prioridad 2 - Verificación de inventario:** confirmar que vCenter, Roller, Expressway, Synology DSM y NGINX estén parcheados o en proceso.
3. 🟡 **General:** revisar logs de los sistemas afectados por CVEs previos; buscar IOCs conocidos (rutas webshell, hashes, IPs).

## 3. Recomendaciones SOC L2

1. 🟠 **Hardening:** mantener segmentación de vCenter, deshabilitar XML-RPC en Roller si no se usa, restringir acceso a Expressway database service.
2. 🟡 **Threat hunting:** buscar actividad de ransomware C3RB3R en Synology DSM; buscar webshells en NGINX; buscar RPC anómalos en Expressway.
3. 🟢 **Tuning de detecciones:** mantener reglas Sigma/YARA existentes; alertar ante nuevas adiciones KEV.
4. 🟢 **Mejoras de proceso:** documentar estado de remediación por CVE antes del deadline 23/11.

## 4. Resumen ejecutivo

- CISA no añadió nuevos CVEs al KEV el 21/11.
- No se detectaron PoC públicos nuevos ni explotación confirmada nueva ese día.
- La prioridad sigue siendo cerrar los CVEs del 18/11 y 20/11 con deadline federal 23/11.
- Vigilancia continua de Shadowserver y VulnCheck KEV para nuevas señales.

## 5. Contexto de seguimiento

- CVE-2024-10441 (Synology DSM) — KEV desde 18/11, deadline 23/11, ransomware C3RB3R. Sin cambios.
- CVE-2026-123007 y hermanos (NGINX) — PoC público. Sin cambios.
- CVE-2026-59310 (VMware vCenter) — KEV desde 20/11, deadline 23/11. Sin cambios.
- CVE-2026-129448 (Apache Roller) — KEV desde 20/11, deadline 23/11. Sin cambios.
- CVE-2026-76466 (Cisco Expressway) — KEV desde 20/11, deadline 23/11. Sin cambios.
- CVE-2025-42884 (SAP NetWeaver EP) — KEV desde 20/11, deadline 23/11. Sin cambios.
- CVE-2026-19868 (SonicWall SonicOS) — KEV desde 06/11. Sin cambios.

## 6. Cobertura y limitaciones

- Fuentes consultadas: CISA KEV, NVD, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
- No se encontraron nuevas adiciones KEV el 21/11.
- Algunos CVEs recientes pueden no tener EPSS disponible aún.

```json
{"fecha":"2026-11-21","cves":[],"otras_amenazas":[]}
```

---

Informe CTI Diario — 2026-11-20

**Fecha del informe:** 21/11/2026  **Cobertura:** novedades del viernes 20 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) · 🟠 Alta (7-8.9) · 🟡 Media (4-6.9) · 🟢 Baja (<4).

> 📌 **Resumen en 30 segundos:** CISA añadió 4 CVEs al KEV el 20/11 con deadline federal 23/11: VMware vCenter (CVE-2026-59310, directory traversal, 9.8), Apache Roller (CVE-2026-129448, deserialización XML-RPC, 9.8), Cisco Expressway (CVE-2026-76466, credenciales hard-coded, 9.8) y SAP NetWeaver Enterprise Portal (CVE-2025-42884, SQL injection, 6.5). Parchear antes del 23/11; asumir compromiso si no es posible.

---

## 1. Turno de vigilancia — qué atender primero

### Top 5 atacar hoy

| # | CVE | Severidad | Acción | Tiempo est. |
|---|---|---|---|---|
| 1 | CVE-2026-59310 (VMware vCenter) | 🔴 9.8 | Parchear vCenter a 9.1.1.0200/9.0.2.0200/8.0 U3m/5.2.5. Ransomware activo. | 1-2 h |
| 2 | CVE-2026-129448 (Apache Roller) | 🔴 9.8 | Actualizar Roller a 6.1.6 o deshabilitar XML-RPC. Pre-auth RCE. | 30 min-1 h |
| 3 | CVE-2026-76466 (Cisco Expressway) | 🔴 9.8 | Actualizar a X15.5.20. Creds hard-coded en DB service. RCE root. | 1-2 h |
| 4 | CVE-2025-42884 (SAP NetWeaver EP) | 🟡 6.5 | Aplicar SAP Note 3660969. SQL injection en portal. | 1-2 h |
| 5 | CVE-2026-19868 (SonicWall SonicOS) | 🟠 — | Actualizar SonicOS a 7.3.4-7013/8.2.3-8015. Bypass SAML MFA. KEV desde 06/11. | 30 min-1 h |

### Prioridades

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 - VMware vCenter (CVE-2026-59310, 9.8, KEV) | Parchear vCenter a 9.1.1.0200 / 9.0.2.0200 / 8.0 U3m / 5.2.5. Directory traversal en Syslog → RCE. Ataque activo por actor China-nexus desplegando ransomware. Deadline federal 23/11. |
| 2 | 🔴 P2 - Apache Roller (CVE-2026-129448, 9.8, KEV) | Actualizar Roller a 6.1.6 o deshabilitar XML-RPC. Deserialización pre-auth en XML-RPC endpoint. Explotación activa confirmada. Deadline federal 23/11. |
| 3 | 🔴 P3 - Cisco Expressway (CVE-2026-76466, 9.8, KEV) | Actualizar a X15.5.20. Credenciales hard-coded en database service de clustered Expressway-C/E. RCE como root sin auth. Deadline federal 23/11. |
| 4 | 🟡 P4 - SAP NetWeaver EP (CVE-2025-42884, 6.5, KEV) | Aplicar SAP Note 3660969. SQL injection en SAP NetWeaver Enterprise Portal. Disclose/modificación de datos. Deadline federal 23/11. |
| 5 | 🟠 P5 - SonicWall SonicOS (CVE-2026-19868, KEV desde 06/11) | Actualizar a 7.3.4-7013 / 8.2.3-8015. Bypass de SAML MFA en SSLVPN. Explotación activa. |
| 6 | 🟡 General | Seguimiento de hallazgos previos (Synology, NGINX, Puppet, REDCap, Axis, LiteLLM). Sin novedades adicionales el 20/11. |

> ℹ️ CISA añadió 4 CVEs al KEV el 20/11 con deadline federal 23/11. Todos son explotación activa confirmada.

---

## 2. Recomendaciones SOC L1

1. 🔴 **Prioridad 1 - VMware vCenter:** inventariar instancias vCenter Server; parchear a 9.1.1.0200 / 9.0.2.0200 / 8.0 U3m / 5.2.5. Revisar logs de Syslog service y buscar actividad de ransomware (archivos cifrados, procesos inusuales en ESXi host).
2. 🔴 **Prioridad 2 - Apache Roller:** inventariar instancias Apache Roller 4.0.0-6.1.5; actualizar a 6.1.6 o deshabilitar XML-RPC. Revisar logs de acceso al endpoint XML-RPC (/xmlrpc) para requests con vendor extension types.
3. 🔴 **Prioridad 3 - Cisco Expressway:** inventariar clustered Expressway-C/E devices; actualizar a X15.5.20. Revisar logs de database service para RPC commands anómalos; buscar accesos no autorizados.
4. 🟡 **Prioridad 4 - SAP NetWeaver EP:** inventariar SAP NetWeaver Enterprise Portal; aplicar SAP Note 3660969. Revisar logs de portal para SQL injection en parámetros de query.
5. 🟠 **Prioridad 5 - SonicWall SonicOS:** inventariar firewalls SonicWall con SSLVPN habilitado; actualizar a 7.3.4-7013 / 8.2.3-8015. Revisar logs de autenticación SSLVPN para bypass de MFA.
6. 🟡 **General:** verificar que no queden sistemas sin parchear tras el deadline del 23/11; asumir compromiso y cazar IOCs si los hay.

---

## 3. Recomendaciones SOC L2

1. 🔴 **Hardening (vCenter):** restringir acceso de red al Syslog service de vCenter; segmentar vCenter de la red de producción; verificar integridad de backups de VMs.
2. 🔴 **Hardening (Roller):** deshabilitar XML-RPC si no se usa; implementar WAF con firmas para deserialización Java en XML-RPC; monitorear tráfico a /xmlrpc.
3. 🟠 **Threat hunting:** buscar en vCenter actividad de ransomware (archivos cifrados, procesos inusuales en ESXi); buscar en Expressway RPC commands anómalos; buscar en SonicWall autenticaciones SSLVPN sin MFA.
4. 🟠 **Tuning de detecciones:** crear reglas Sigma para deserialización Java en XML-RPC (Apache Roller); alertar sobre accesos al Syslog service de vCenter desde IPs no reconocidas; detectar RPC commands anómalos en Expressway database service.
5. 🟡 **Automatización:** integrar KEV en pipeline de priorización; alertar automáticamente ante nuevas adiciones con deadline federal; monitorear NVD para CVEs con PoC público en Java deserialization.
6. 🟡 **Mejoras de proceso:** documentar estado de remediación por CVE; usar fechas KEV (BOD 26-04) como SLA interno; mantener inventario actualizado de software expuesto a Internet (vCenter, Roller, Expressway, SonicWall).

---

## 4. Resumen ejecutivo

- **4 nuevos CVEs en KEV el 20/11:** VMware vCenter (CVE-2026-59310, 9.8, directory traversal en Syslog), Apache Roller (CVE-2026-129448, 9.8, deserialización XML-RPC pre-auth), Cisco Expressway (CVE-2026-76466, 9.8, creds hard-coded), SAP NetWeaver EP (CVE-2025-42884, 6.5, SQL injection). Deadline federal 23/11.
- **vCenter bajo ataque activo:** actor China-nexus desplegando ransomware mediante CVE-2026-59310. Parche disponible desde agosto 2026 pero muchos sistemas siguen sin actualizar.
- **Apache Roller con RCE pre-auth:** deserialización de vendor extension types en XML-RPC antes de autenticación. Afecta versiones 4.0.0-6.1.4. Fix en 6.1.6.
- **Cisco Expressway RCE como root:** credenciales hard-coded en database service de clustered devices. Sin workaround; solo parche X15.5.20.
- **SonicWall SonicOS sigue en KEV:** CVE-2026-19868 (bypass SAML MFA en SSLVPN). Explotación activa desde 06/11. Actualizar a 7.3.4-7013/8.2.3-8015.

---

## 5. Novedades del 20/11

### 5.1 KEV / explotación confirmada

| CVE | CVSS | Producto | Impacto | EPSS |
|---|---|---|---|---|
| 🔴 CVE-2026-59310 | 9.8 (v3) | VMware vCenter | Directory traversal en Syslog → RCE. Ransomware activo. | - |
| 🔴 CVE-2026-129448 | 9.8 (v3) | Apache Roller | Deserialización XML-RPC pre-auth → RCE. | - |
| 🔴 CVE-2026-76466 | 9.8 (v3) | Cisco Expressway | Creds hard-coded en DB service → RCE root. | - |
| 🟡 CVE-2025-42884 | 6.5 (v3) | SAP NetWeaver EP | SQL injection → disclose/modificación de datos. | - |

> ℹ️ CISA añadió los 4 CVEs al KEV el 20/11 con deadline federal 23/11. Explotación activa confirmada para todos.

### 5.2 Hallazgos por CVE (detalle)

**CVE-2026-59310 — VMware vCenter Server (Directory Traversal en Syslog)**

- **CVSS:** 9.8 (Crítica, CVSS 3.1). **CWE-22** (improper limitation of a pathname to a restricted directory). **EPSS:** no disponible en fuentes consultadas.
- **Producto afectado:** VMware vCenter Server (versiones no parcheadas). Fix en 9.1.1.0200, 9.0.2.0200, 8.0 U3m, 5.2.5 (Broadcom VMSA-2026-0006.1).
- **Explotación:** activa (KEV el 20/11). Actor China-nexus desplegando ransomware mediante directory traversal en Syslog service. Parche disponible desde agosto 2026.
- **Impacto:** RCE en vCenter Server; acceso a infraestructura de virtualización; despliegue de ransomware en VMs y ESXi hosts.
- **Mitigación:** actualizar vCenter a la versión fija de la línea correspondiente. Revisar logs de Syslog service y buscar actividad de ransomware.
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1486 (Data Encrypted for Impact).
- **IOCs:** archivos cifrados en VMs/ESXi hosts; procesos inusuales en ESXi; actividad anómala en Syslog service de vCenter.
- **Sigma:** `title: VMware vCenter Syslog Directory Traversal (CVE-2026-59310) | description: Detects anomalous file access patterns in vCenter Syslog service indicative of directory traversal | logsource: file_event | detection: selection: TargetFilename|contains: '../' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión vCenter instalada; revisar logs de Syslog service; buscar archivos cifrados o procesos inusuales en ESXi hosts.
  - **Contención:** aislar vCenter de la red si se sospecha compromiso; deshabilitar acceso remoto innecesario; verificar integridad de backups de VMs.
  - **Verificación:** confirmar versión vCenter parcheada; validar ausencia de actividad anómala post-parche; auditar VMs y ESXi hosts.

**CVE-2026-129448 — Apache Roller (Deserialización de datos no confiables en XML-RPC)**

- **CVSS:** 9.8 (Crítica, CVSS 3.1). **CWE-502** (deserialización de datos no confiables). **EPSS:** no disponible en fuentes consultadas.
- **Producto afectado:** Apache Roller 4.0.0 through 6.1.4 (también CVE-2026-82384 para 6.1.5, mismo bug). Fix en 6.1.6.
- **Explotación:** activa (KEV el 20/11). Deserialización de vendor extension types en XML-RPC endpoint antes de autenticación. Explotación confirmada en la calle.
- **Impacto:** RCE unauth en el servidor que ejecuta Apache Roller; acceso al sistema host y datos del blog.
- **Mitigación:** actualizar a Apache Roller 6.1.6 o deshabilitar XML-RPC si no se usa. Implementar WAF con firmas para deserialización Java.
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1059 (Command and Scripting Interpreter).
- **IOCs:** requests HTTP POST a /xmlrpc con vendor extension types en el body; procesos hijo inesperados de la JVM de Roller.
- **Sigma:** `title: Apache Roller XML-RPC Deserialization (CVE-2026-129448) | description: Detects HTTP POST requests to Apache Roller XML-RPC endpoint with suspicious vendor extension types | logsource: web | detection: selection: cs-method: 'POST' AND cs-uri-stem|contains: '/xmlrpc' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión Roller instalada; revisar logs de acceso a /xmlrpc; buscar procesos hijo inesperados de la JVM.
  - **Contención:** actualizar a 6.1.6 o deshabilitar XML-RPC; implementar WAF con firmas; segmentar el servidor Roller.
  - **Verificación:** confirmar versión parcheada; validar ausencia de actividad anómala post-parche; auditar logs de acceso.

**CVE-2026-76466 — Cisco Expressway Series (Credenciales hard-coded en database service)**

- **CVSS:** 9.8 (Crítica, CVSS 3.1). **CWE-798** (use of hard-coded credentials). **EPSS:** no disponible en fuentes consultadas.
- **Producto afectado:** Cisco Expressway-C (Core) y Expressway-E (Edge) clustered devices. Fix en X15.5.20.
- **Explotación:** activa (KEV el 20/11). RCE como root sin autenticación mediante RPC commands al database service. Explotación confirmada (SecurityWeek, SecurityAffairs).
- **Impacto:** RCE como root en el dispositivo Expressway; acceso a credenciales y datos sensibles; compromiso de la infraestructura de comunicaciones unificadas.
- **Mitigación:** actualizar a X15.5.20. No hay workaround disponible. Revisar logs de database service para RPC commands anómalos.
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1078 (Valid Accounts) por el uso de credenciales hard-coded.
- **IOCs:** RPC commands anómalos al database service de Expressway; accesos no autorizados a Expressway-C/E; procesos hijo inesperados.
- **Sigma:** `title: Cisco Expressway DB Service RCE (CVE-2026-76466) | description: Detects anomalous RPC commands to Expressway database service indicative of exploitation | logsource: network | detection: selection: destination_port: 0 AND protocol: 'RPC' | condition: selection`
- **Playbook:**
  - **Triage:** inventariar clustered Expressway-C/E devices; verificar versión instalada; revisar logs de database service.
  - **Contención:** actualizar a X15.5.20; restringir acceso de red al database service; segmentar dispositivos Expressway.
  - **Verificación:** confirmar versión parcheada; validar ausencia de RPC commands anómalos post-parche; auditar accesos.

**CVE-2025-42884 — SAP NetWeaver Enterprise Portal (SQL injection)**

- **CVSS:** 6.5 (Media, CVSS 3.1). **CWE-89** (improper neutralization of special elements used in an SQL command). **EPSS:** no disponible en fuentes consultadas.
- **Producto afectado:** SAP NetWeaver Enterprise Portal. Fix en SAP Note 3660969.
- **Explotación:** activa (KEV el 20/11). SQL injection en data query logic; permite a atacante no autenticado divulgar o modificar datos.
- **Impacto:** divulgación o modificación de datos en el portal SAP NetWeaver; posible escalación de privilegios.
- **Mitigación:** aplicar SAP Note 3660969. Revisar logs de portal para SQL injection en parámetros de query.
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1078 (Valid Accounts) si se obtienen credenciales.
- **IOCs:** requests HTTP con parámetros SQL sospechosos en el portal SAP NetWeaver; errores SQL en logs del portal.
- **Sigma:** `title: SAP NetWeaver Portal SQL Injection (CVE-2025-42884) | description: Detects HTTP requests to SAP NetWeaver Enterprise Portal with suspicious SQL parameters | logsource: web | detection: selection: cs-uri-stem|contains: 'portal' AND cs-uri-query|contains: 'SELECT' OR cs-uri-query|contains: 'UNION' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión SAP NetWeaver EP instalada; revisar logs de portal para SQL injection; buscar errores SQL anómalos.
  - **Contención:** aplicar SAP Note 3660969; restringir acceso al portal; segmentar el servidor SAP.
  - **Verificación:** confirmar nota SAP aplicada; validar ausencia de SQL injection post-parche; auditar datos del portal.

**CVE-2026-19868 — SonicWall SonicOS SSLVPN (Bypass de SAML MFA)**

- **CVSS:** no confirmado en NVD (CWE-308, use of single-factor authentication). **EPSS:** no disponible.
- **Producto afectado:** SonicWall SonicOS (Gen7/Gen8 firewalls) con SSLVPN habilitado. Fix en 7.3.4-7013 / 8.2.3-8015.
- **Explotación:** activa (KEV desde 06/11). Bypass de SAML MFA en SSLVPN; usuario con credenciales válidas para método de auth alternativo puede evadir controles SAML/MFA.
- **Impacto:** acceso no autorizado a la VPN; bypass de MFA; compromiso de la red interna.
- **Mitigación:** actualizar a 7.3.4-7013 / 8.2.3-8015. Revisar logs de autenticación SSLVPN para bypass de MFA.
- **MITRE ATT&CK:** T1078 (Valid Accounts) y T1133 (External Remote Services).
- **IOCs:** autenticaciones SSLVPN exitosas sin MFA; accesos a VPN desde IPs no reconocidas.
- **Sigma:** `title: SonicWall SSLVPN MFA Bypass (CVE-2026-19868) | description: Detects SSLVPN authentications that bypass SAML MFA controls | logsource: firewall | detection: selection: event_type: 'sslvpn_auth' AND mfa_status: 'bypassed' | condition: selection`
- **Playbook:**
  - **Triage:** inventariar firewalls SonicWall con SSLVPN; verificar versión SonicOS; revisar logs de auth SSLVPN.
  - **Contención:** actualizar a 7.3.4-7013/8.2.3-8015; deshabilitar SSLVPN si no se usa; forzar MFA en todos los métodos de auth.
  - **Verificación:** confirmar versión parcheada; validar que MFA se aplique en todos los métodos; auditar accesos VPN.

---

## 6. Contexto de seguimiento (sin cambios respecto al 18/11)

- **CVE-2026-115731 (Puppet Enterprise):** deadline federal 01/12. Sin novedades el 20/11.
- **CVE-2024-10441 (Synology DSM):** ransomware C3RB3R activo. Sin novedades el 20/11.
- **CVE-2026-123007 y hermanos (NGINX):** PoC público. Sin novedades el 20/11.
- **CVE-2026-90817 (REDCap):** deadline federal 21/11. Sin novedades el 20/11.
- **CVE-2026-90436 (Apache Axis 1.x):** deadline federal 21/11. Sin novedades el 20/11.
- **CVE-2026-40217 (LiteLLM):** deadline federal 02/12. Sin novedades el 20/11.
- **CVE-2026-124194 (Outlook, NTLMv2 leak):** sin cambios.
- **Dispositivos EOL (D-Link, DrayTek):** sin cambios; pendiente de reemplazo.

---

## 7. Cobertura y limitaciones

- CISA añadió 4 CVEs al KEV el 20/11 (CVE-2026-59310, CVE-2026-129448, CVE-2026-76466, CVE-2025-42884) con deadline federal 23/11.
- CVE-2026-19868 (SonicWall SonicOS) sigue en KEV desde 06/11; incluido por explotación activa continua.
- EPSS no disponible para los CVEs del 20/11 en las fuentes consultadas.
- Cobertura centrada en KEV y CVEs críticos con PoC público. No se detectaron otras adiciones oficiales al KEV el 20/11.
- CISA no publicó IOCs para los 4 CVEs añadidos al KEV el 20/11.
- Nota: la entrada del 19/11 no se encontró en el documento; puede haberse omitido en una corrida anterior. Se recomienda verificar.

```json
{"fecha":"2026-11-20","cves":[{"id":"CVE-2026-59310","producto":"VMware vCenter Server","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar vCenter a 9.1.1.0200 / 9.0.2.0200 / 8.0 U3m / 5.2.5.","runbook":"1) Inventariar instancias vCenter. 2) Parchear a version fija. 3) Revisar logs de Syslog service. 4) Buscar actividad de ransomware en VMs/ESXi.","epss":null,"iocs":["archivos cifrados en VMs/ESXi","procesos inusuales en ESXi","actividad anomala en Syslog service"],"sigma":"Regla Sigma: deteccion de patrones de acceso a archivos anormal en Syslog service de vCenter indicativos de directory traversal.","playbook":{"triage":["Verificar version vCenter instalada.","Revisar logs de Syslog service.","Buscar archivos cifrados o procesos inusuales en ESXi hosts."],"contencion":["Aislar vCenter de la red si se sospecha compromiso.","Deshabilitar acceso remoto innecesario.","Verificar integridad de backups de VMs."],"verificacion":["Confirmar version vCenter parcheada.","Validar ausencia de actividad anomala post-parche.","Auditar VMs y ESXi hosts."]}},{"id":"CVE-2026-129448","producto":"Apache Roller","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a Apache Roller 6.1.6 o deshabilitar XML-RPC.","runbook":"1) Inventariar instancias Apache Roller 4.0.0-6.1.5. 2) Actualizar a 6.1.6 o deshabilitar XML-RPC. 3) Revisar logs de /xmlrpc. 4) Buscar procesos hijo inesperados de JVM.","epss":null,"iocs":["requests HTTP POST a /xmlrpc con vendor extension types","procesos hijo inesperados de JVM de Roller"],"sigma":"Regla Sigma: deteccion de requests HTTP POST a endpoint XML-RPC de Apache Roller con vendor extension types sospechosos.","playbook":{"triage":["Verificar version Roller instalada.","Revisar logs de acceso a /xmlrpc.","Buscar procesos hijo inesperados de la JVM."],"contencion":["Actualizar a 6.1.6 o deshabilitar XML-RPC.","Implementar WAF con firmas.","Segmentar el servidor Roller."],"verificacion":["Confirmar version parcheada.","Validar ausencia de actividad anomala post-parche.","Auditar logs de acceso."]}},{"id":"CVE-2026-76466","producto":"Cisco Expressway Series","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a X15.5.20. Sin workaround disponible.","runbook":"1) Inventariar clustered Expressway-C/E devices. 2) Actualizar a X15.5.20. 3) Revisar logs de database service. 4) Buscar RPC commands anomalos.","epss":null,"iocs":["RPC commands anomalos al database service","accesos no autorizados a Expressway-C/E","procesos hijo inesperados"],"sigma":"Regla Sigma: deteccion de RPC commands anomalos al database service de Expressway indicativos de explotacion.","playbook":{"triage":["Inventariar clustered Expressway-C/E devices.","Verificar version instalada.","Revisar logs de database service."],"contencion":["Actualizar a X15.5.20.","Restringir acceso de red al database service.","Segmentar dispositivos Expressway."],"verificacion":["Confirmar version parcheada.","Validar ausencia de RPC commands anomalos post-parche.","Auditar accesos."]}},{"id":"CVE-2025-42884","producto":"SAP NetWeaver Enterprise Portal","cvss":6.5,"severidad":"Media","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Aplicar SAP Note 3660969.","runbook":"1) Inventariar SAP NetWeaver Enterprise Portal. 2) Aplicar SAP Note 3660969. 3) Revisar logs de portal para SQL injection. 4) Buscar errores SQL anomalos.","epss":null,"iocs":["requests HTTP con parametros SQL sospechosos en portal","errores SQL en logs del portal"],"sigma":"Regla Sigma: deteccion de requests HTTP a SAP NetWeaver Enterprise Portal con parametros SQL sospechosos.","playbook":{"triage":["Verificar version SAP NetWeaver EP instalada.","Revisar logs de portal para SQL injection.","Buscar errores SQL anomalos."],"contencion":["Aplicar SAP Note 3660969.","Restringir acceso al portal.","Segmentar el servidor SAP."],"verificacion":["Confirmar nota SAP aplicada.","Validar ausencia de SQL injection post-parche.","Auditar datos del portal."]}},{"id":"CVE-2026-19868","producto":"SonicWall SonicOS SSLVPN","cvss":null,"severidad":"Alta","mitre":{"id":"T1078","nombre":"Valid Accounts"},"explotacion":"KEV","parche":"Actualizar a 7.3.4-7013 / 8.2.3-8015.","runbook":"1) Inventariar firewalls SonicWall con SSLVPN. 2) Actualizar a 7.3.4-7013 / 8.2.3-8015. 3) Revisar logs de auth SSLVPN. 4) Buscar bypass de MFA.","epss":null,"iocs":["autenticaciones SSLVPN exitosas sin MFA","accesos a VPN desde IPs no reconocidas"],"sigma":"Regla Sigma: deteccion de autenticaciones SSLVPN que evaden controles SAML MFA.","playbook":{"triage":["Inventariar firewalls SonicWall con SSLVPN.","Verificar version SonicOS.","Revisar logs de auth SSLVPN."],"contencion":["Actualizar a 7.3.4-7013 / 8.2.3-8015.","Deshabilitar SSLVPN si no se usa.","Forzar MFA en todos los metodos de auth."],"verificacion":["Confirmar version parcheada.","Validar que MFA se aplique en todos los metodos.","Auditar accesos VPN."]}}],"otras_amenazas":[]}
```

---

Ahora inserto esto en el documento. La entrada más reciente actual es del 18/11. Necesito poner la del 20/11 arriba.<|end|><|start|><|start|>assistant to=shell<|channel|>analysis <|constrain|>xml<|message|><rfl:parameter name="command">
cd "F:\Repositorios\CyberThreatIntelligence" && ls -la

---

# 🔴 Informe CTI Diario — 2026-11-18

**Fecha del informe:** 19/11/2026  **Cobertura:** novedades del martes 18 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) · 🟠 Alta (7-8.9) · 🟡 Media (4-6.9) · 🟢 Baja (<4).

> 📌 **Resumen en 30 segundos:** CISA añadió 4 CVEs al KEV el 18/11: Synology DSM (CVE-2024-10441, CVSS 9.8, explotado por ransomware C3RB3R), LiteLLM (CVE-2026-40217, RCE), Apache Axis 1.x (CVE-2026-90436, RCE unauth), REDCap (CVE-2026-90817, RCE unauth). Además se publicaron 5 CVEs críticos de NGINX (CVE-2026-123007, CVSS 9.2) con PoC público y explotación confirmada en cloud. Priorizar Synology (ransomware activo) y NGINX (PoC público).

---

## 1. Turno de vigilancia — qué atender primero

### Top 5 atacar hoy

| # | CVE | Severidad | Acción | Tiempo est. |
|---|---|---|---|---|
| 1 | CVE-2024-10441 (Synology DSM) | 🔴 9.8 | Parchear DSM; si no es posible, aislar del Internet. Ransomware C3RB3R activo. | 1-2 h |
| 2 | CVE-2026-123007 (NGINX HTTP/2) | 🔴 9.2 | Parchear a 1.31.9/1.30.7 o deshabilitar HTTP/2. PoC público. | 30 min-1 h |
| 3 | CVE-2026-90817 (REDCap) | 🔴 ~9.8 | Actualizar a 16.0.49/17.3.10/17.4.4. RCE unauth. | 1-2 h |
| 4 | CVE-2026-90436 (Apache Axis 1.x) | 🔴 Crítico | Eliminar Axis 1.x del classpath o actualizar Confluence/N-central. | 2-4 h |
| 5 | CVE-2026-40217 (LiteLLM) | 🟠 8.8 | Actualizar LiteLLM post 2026-04-08; restringir /guardrails/test_custom_code. | 30 min-1 h |

### Prioridades

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 - Synology DSM (CVE-2024-10441, 9.8, KEV) | Parchear DSM a la versión fija; si no es posible, aislar del Internet. Ransomware C3RB3R desplegándose contra NAS sin parchear. Deadline federal 21/11. |
| 2 | 🔴 P2 - NGINX (CVE-2026-123007 y hermanos, 9.2, PoC público) | Parchear a 1.31.9/1.30.7 o deshabilitar HTTP/2 como mitigación temporal. PoC público en GitHub; explotación confirmada en cloud. |
| 3 | 🔴 P3 - REDCap (CVE-2026-90817, ~9.8, KEV) | Actualizar a 16.0.49 / 17.3.10 / 17.4.4. RCE unauth con hash de encuesta pública. Deadline federal 21/11. |
| 4 | 🟠 P4 - Apache Axis 1.x (CVE-2026-90436, Crítico, KEV) | Eliminar Axis 1.x del classpath de Confluence/N-central u otras apps; Axis 1.x es EOL. Deadline federal 21/11. |
| 5 | 🟠 P5 - LiteLLM (CVE-2026-40217, 8.8, KEV) | Actualizar LiteLLM a versión posterior a 2026-04-08; restringir endpoint /guardrails/test_custom_code. Deadline federal 02/12. |
| 6 | 🟡 General | Seguimiento de hallazgos previos (Puppet Enterprise, Check Point, UnrealIRCd, dispositivos EOL). Sin novedades adicionales el 18/11. |

> ℹ️ CISA añadió 4 CVEs al KEV el 18/11. NGINX no está en KEV aún pero tiene PoC público y explotación confirmada en investigación.

---

## 2. Recomendaciones SOC L1

1. 🔴 **Prioridad 1 - Synology DSM:** inventariar NAS Synology (versión DSM); parchear a la versión fija de la línea (7.2.2-72806-1, 7.2.1-69057-6, 7.2-64570-4, 7.1.1-42962-7). Si no es posible, aislar del Internet y verificar integridad de backups. Buscar indicadores de C3RB3R (archivos .c3rb3r, ransom note).
2. 🔴 **Prioridad 2 - NGINX:** inventariar servidores NGINX con HTTP/2 habilitado; parchear a 1.31.9 (mainline) o 1.30.7 (stable). Como mitigación temporal, deshabilitar HTTP/2 (`listen 443 ssl;` sin `http2`). Verificar que no haya proxies HTTP/2 terminando delante.
3. 🔴 **Prioridad 3 - REDCap:** inventariar instancias REDCap ≥13.3.0; actualizar a 16.0.49 / 17.3.10 / 17.4.4. Revisar logs de acceso a endpoints de survey passthrough (__passthru) y Data Import.
4. 🟠 **Prioridad 4 - Apache Axis:** buscar JARs de org.apache.axis:axis en classpaths de Confluence, N-central y otras apps Java. Eliminar o reemplazar. Axis 1.x es EOL sin parche.
5. 🟠 **Prioridad 5 - LiteLLM:** inventariar instancias LiteLLM; actualizar a versión posterior a 2026-04-08. Restringir acceso al endpoint /guardrails/test_custom_code.
6. 🟡 **General:** verificar que no queden sistemas Synology sin parchear tras el deadline del 21/11; asumir compromiso y cazar IOCs si los hay.

## 3. Recomendaciones SOC L2

1. 🔴 **Hardening (Synology):** deshabilitar acceso remoto innecesario a NAS; habilitar 2FA en cuentas admin; segmentar NAS de la red de producción; verificar integridad de backups offline.
2. 🔴 **Hardening (NGINX):** deshabilitar HTTP/2 donde no sea necesario; implementar WAF/IPS con firmas para CVE-2026-123007; monitorear tráfico HTTP/2 anómalo (volumen alto de control frames).
3. 🟠 **Threat hunting:** buscar en logs de NGINX conexiones HTTP/2 con volumen anómalo de frames (indicador de explotación); buscar en Synology actividad de C3RB3R (archivos .c3rb3r, procesos inusuales).
4. 🟠 **Tuning de detecciones:** crear reglas Sigma para tráfico HTTP/2 anómalo (alto volumen de WINDOW_UPDATE/RST_STREAM); alertar sobre acceso a /guardrails/test_custom_code en LiteLLM; detectar JARs de Axis 1.x en inventario de software.
5. 🟡 **Automatización:** integrar KEV en pipeline de priorización; alertar automáticamente ante nuevas adiciones con deadline federal; monitorear NVD para CVEs con PoC público en HTTP/2.
6. 🟡 **Mejoras de proceso:** documentar estado de remediación por CVE; usar fechas KEV (BOD 26-04) como SLA interno; mantener inventario actualizado de software expuesto a Internet (NGINX, Synology, REDCap).

---

## 4. Resumen ejecutivo

- **4 nuevos CVEs en KEV el 18/11:** Synology DSM (CVE-2024-10441, 9.8, ransomware C3RB3R), LiteLLM (CVE-2026-40217, RCE), Apache Axis 1.x (CVE-2026-90436, RCE unauth), REDCap (CVE-2026-90817, RCE unauth). Deadlines federales 21/11 y 02/12.
- **NGINX crítico con PoC público:** CVE-2026-123007 (CVSS 9.2) y 4 CVEs hermanos afectan NGINX 1.9.5-1.31.8 con HTTP/2. PoC público en GitHub; explotación confirmada en cloud. Parche disponible (1.31.9/1.30.7).
- **Ransomware activo contra Synology:** C3RB3R está desplegándose contra NAS Synology sin parchear con CVE-2024-10441 (parcheado desde dic 2024 pero muchos dispositivos siguen sin actualizar).
- **Axis 1.x es EOL:** sin parche disponible; la única mitigación es eliminarlo del classpath. Afecta Confluence Data Center y N-able N-central.
- **REDCap expone datos de investigación:** RCE unauth con hash de encuesta pública; afecta plataformas de captura de datos de investigación y clínicos.

---

## 5. Novedades del 18/11

### 5.1 KEV / explotación confirmada

| CVE | CVSS | Producto | Impacto | EPSS |
|---|---|---|---|---|
| 🔴 CVE-2024-10441 | 9.8 (v3) | Synology DSM | RCE via improper encoding en system plugin daemon → C3RB3R ransomware | - |
| 🔴 CVE-2026-90817 | ~9.8 (v3) | REDCap | RCE unauth via survey passthrough routing + Data Import | - |
| 🔴 CVE-2026-90436 | Crítico (v3) | Apache Axis 1.x | RCE unauth via unsafe reflection (CWE-470) en SOAP fault handling | - |
| 🟠 CVE-2026-40217 | 8.8 (v3) | LiteLLM | RCE via bytecode rewriting en /guardrails/test_custom_code | - |

### 5.2 Hallazgos por CVE (detalle)

**CVE-2024-10441 — Synology DiskStation Manager (DSM) (Improper Encoding/Escaping of Output)**

- **CVSS:** 9.8 (Crítica, CVSS 3.1). **CWE-116** (improper encoding or escaping of output). **EPSS:** no disponible.
- **Producto afectado:** Synology DSM < 7.2.2-72806-1, < 7.2.1-69057-6, < 7.2-64570-4, < 7.1.1-42962-7, < 6.2.4-25556-8. También BeeStation OS (BSM) < 1.1-65374.
- **Explotación:** activa (KEV el 18/11). C3RB3R ransomware desplegándose contra NAS sin parchear. Parche disponible desde dic 2024 (Synology-SA-24:20).
- **Impacto:** RCE como root en el NAS; acceso a datos almacenados; despliegue de ransomware C3RB3R con cifrado de archivos y nota de rescate.
- **Mitigación:** actualizar DSM a la versión fija de la línea correspondiente; si no es posible, aislar del Internet. Verificar integridad de backups offline.
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1486 (Data Encrypted for Impact).
- **IOCs:** archivos con extensión .c3rb3r; ransom note típica de C3RB3R; procesos inusuales en NAS.
- **Sigma:** `title: Synology DSM C3RB3R Ransomware Activity | description: Detects file creation with .c3rb3r extension or C3RB3R ransom note on Synology DSM | logsource: file_event | detection: selection: TargetFilename|endswith: '.c3rb3r' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión DSM instalada; buscar archivos .c3rb3r o ransom notes; revisar logs de acceso al NAS.
  - **Contención:** aislar NAS de la red; deshabilitar acceso remoto; verificar integridad de backups.
  - **Verificación:** confirmar versión DSM parcheada; validar ausencia de archivos cifrados; restaurar desde backup limpio si es necesario.

**CVE-2026-90817 — Vanderbilt University REDCap (Code Injection / RCE)**

- **CVSS:** ~9.8 (Crítica, CVSS 3.1, cifra de fuente única). **CWE-73** (external control of file name or path). **EPSS:** no disponible.
- **Producto afectado:** REDCap 13.3.0 y superior. Corregido en 16.0.49, 17.3.10 y 17.4.4.
- **Explotación:** activa (KEV el 18/11). Requiere conocimiento de un hash de encuesta pública válido.
- **Impacto:** RCE unauth en el servidor REDCap; exposición de datos de investigación y clínicos. Ejecución como proceso web (www-data).
- **Mitigación:** actualizar a 16.0.49 / 17.3.10 / 17.4.4. Revisar logs de acceso a endpoints de survey passthrough (__passthru) y Data Import.
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1059 (Command and Scripting Interpreter).
- **IOCs:** requests HTTP anómalos a endpoints __passthru con parámetros de file-path o stream manipulados; procesos hijo inesperados del servidor web.
- **Sigma:** `title: REDCap Survey Passthrough RCE Attempt | description: Detects HTTP requests to REDCap __passthru endpoint with suspicious file-path parameters | logsource: web | detection: selection: cs-uri-stem|contains: '__passthru' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión REDCap instalada; revisar logs de acceso a __passthru y Data Import; buscar procesos hijo inesperados del servidor web.
  - **Contención:** actualizar REDCap; restringir acceso a encuestas públicas si no son necesarias; segmentar el servidor REDCap.
  - **Verificación:** confirmar versión parcheada; validar ausencia de actividad anómala en logs post-parche.

**CVE-2026-90436 — Apache Axis 1.x (Unsafe Reflection / RCE)**

- **CVSS:** Crítico (CVSS 3.1, cifra no confirmada en NVD; Snyk reporta 10.0). **CWE-470** (use of externally-controlled input to select classes or code). **EPSS:** no disponible.
- **Producto afectado:** Apache Axis 1.x (todas las versiones hasta 1.4, EOL). Afecta apps que usan Axis 1.x: Confluence Data Center (XML-RPC/SOAP APIs), N-able N-central.
- **Explotación:** activa (KEV el 18/11). Unauth RCE via SOAP Fault handling; el atacante envía un SOAP Fault con `<exceptionName>` que nombra una clase arbitraria. Rapid7 detectó explotación contra Confluence Data Center y N-central el 06/11. Huntress reportó 2 intentos de explotación el 12/11.
- **Impacto:** RCE unauth en el servidor que ejecuta Axis 1.x; compromiso de la app host (Confluence, N-central).
- **Mitigación:** no hay parche para Axis 1.x (EOL). Eliminar Axis 1.x del classpath o actualizar la app host. N-able deshabilitó la feature "Legacy API" que exponía la superficie de ataque.
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1059.006 (Command and Scripting Interpreter: Python) — MITRE no documenta técnica específica para unsafe reflection; T1190 es la más cercana.
- **IOCs:** requests SOAP con `<exceptionName>` sospechoso en el body; procesos hijo inesperados de la JVM de Confluence/N-central.
- **Sigma:** `title: Apache Axis Unsafe Reflection Exploit Attempt | description: Detects SOAP requests containing exceptionName element targeting Axis 1.x | logsource: web | detection: selection: cs-uri-stem|contains: 'axis' | condition: selection`
- **Playbook:**
  - **Triage:** buscar JARs de org.apache.axis:axis en classpaths; revisar logs de acceso SOAP en Confluence/N-central; buscar procesos hijo inesperados de la JVM.
  - **Contención:** eliminar Axis 1.x del classpath; deshabilitar Legacy API en N-central; segmentar servidores afectados.
  - **Verificación:** confirmar ausencia de JARs de Axis 1.x; validar que no haya actividad SOAP anómala post-mitigación.

**CVE-2026-40217 — BerriAI LiteLLM (Unprotected Alternate Channel / RCE)**

- **CVSS:** 8.8 (Alta, CVSS 3.1, Red Hat). **CWE-470** (use of externally-controlled input to select classes or code) o CWE-94 (improper control of code generation). **EPSS:** no disponible.
- **Producto afectado:** LiteLLM ≤ 2026-04-08. Afecta proxy server para APIs de LLM.
- **Explotación:** activa (KEV el 18/11). RCE via bytecode rewriting en endpoint /guardrails/test_custom_code. Requiere admin key de LiteLLM.
- **Impacto:** RCE como root en el contenedor LiteLLM (default Docker image corre como root); acceso a claves de API de LLM, configuración, logs de auditoría.
- **Mitigación:** actualizar LiteLLM a versión posterior a 2026-04-08; restringir acceso al endpoint /guardrails/test_custom_code; no exponer LiteLLM a Internet sin autenticación fuerte.
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1078 (Valid Accounts) por el uso de admin key.
- **IOCs:** requests POST a /guardrails/test_custom_code; actividad anómala en el contenedor LiteLLM; procesos hijo inesperados.
- **Sigma:** `title: LiteLLM Guardrails Code Execution Attempt | description: Detects POST requests to /guardrails/test_custom_code endpoint | logsource: web | detection: selection: cs-method: 'POST' AND cs-uri-stem|contains: '/guardrails/test_custom_code' | condition: selection`
- **Playbook:**
  - **Triage:** verificar versión LiteLLM; revisar logs de acceso a /guardrails/test_custom_code; buscar procesos hijo inesperados en el contenedor.
  - **Contención:** actualizar LiteLLM; restringir endpoint; rotar claves de API de LLM si se sospecha compromiso.
  - **Verificación:** confirmar versión parcheada; validar ausencia de actividad anómala post-parche; auditar claves de API.

### 5.3 Hallazgos adicionales (no KEV pero críticos)

**CVE-2026-123007 y hermanos — NGINX (HTTP/2 Memory Corruption / RCE)**

- **CVEs:** CVE-2026-123007 (heap buffer overflow, HTTP/2), CVE-2026-123633 (heap buffer overflow, HTTP/2), CVE-2026-123634 (use-after-free, HTTP/2), CVE-2026-121810 (integer overflow, HTTP/2), CVE-2026-113425 (use-after-free, mail proxy).
- **CVSS:** 9.2 (Crítico, CVSS 4.0) / 8.1 (Alta, CVSS 3.1) para todos. **EPSS:** no disponible.
- **Producto afectado:** NGINX 1.9.5-1.30.6 y 1.31.0-1.31.8 (HTTP/2); mail proxy 0.5.33-1.30.6 y 1.31.0-1.31.8. NGINX Plus R34-R36. NGINX Gateway Fabric 2.0.0-2.8.1.
- **Explotación:** PoC público en GitHub (sfewer-r7/CVE-2026-123007) con RCE unauth completo contra NGINX 1.31.8. Explotación confirmada en cloud (AWS, GCP, Azure). Requiere ~128 GB de tráfico HTTP/2 y derrota de ASLR.
- **Impacto:** RCE como proceso worker de NGINX; exposición de claves TLS, payloads HTTPS, tokens de autenticación, cookies de sesión, datos proxeados.
- **Mitigación:** actualizar a 1.31.9 (mainline) o 1.30.7 (stable). Mitigación temporal: deshabilitar HTTP/2 (`listen 443 ssl;` sin `http2`). Nota: deshabilitar HTTP/2 solo protege contra CVE-2026-123007 y CVE-2026-123633, no contra los otros 3.
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1499 (Endpoint Denial of Service) para el crash del worker.
- **IOCs:** tráfico HTTP/2 con volumen anómalo de control frames (WINDOW_UPDATE, RST_STREAM); conexiones HTTP/2 de larga duración con alto volumen de datos.
- **Sigma:** `title: NGINX HTTP/2 Anomalous Traffic (CVE-2026-123007) | description: Detects high volume of HTTP/2 control frames indicative of exploitation attempt | logsource: network | detection: selection: protocol: 'HTTP/2' AND frame_type: 'WINDOW_UPDATE' OR frame_type: 'RST_STREAM' | condition: selection | count: >1000 per minute`
- **Playbook:**
  - **Triage:** inventariar servidores NGINX con HTTP/2 habilitado; verificar versión instalada; revisar logs de acceso HTTP/2.
  - **Contención:** parchear a 1.31.9/1.30.7; deshabilitar HTTP/2 como mitigación temporal; implementar IPS con firmas para CVE-2026-123007.
  - **Verificación:** confirmar versión parcheada; validar que HTTP/2 esté deshabilitado si no se parcheó; monitorear tráfico HTTP/2 post-mitigación.

---

## 6. Contexto de seguimiento (sin cambios respecto al 17/11)

- **CVE-2026-115731 (Puppet Enterprise):** deadline federal 01/12. Sin novedades el 18/11.
- **CVE-2026-116779 (FreeSWITCH):** en KEV desde 09/11. Sin novedades el 18/11.
- **CVE-2026-123039 (Check Point Gaia) y CVE-2026-114713 (UnrealIRCd):** deadline federal 16/11 cumplido. Sin novedades.
- **CVE-2026-124194 (Outlook, NTLMv2 leak):** sin cambios.
- **Dispositivos EOL (D-Link, DrayTek):** sin cambios; pendiente de reemplazo.

---

## 7. Cobertura y limitaciones

- CISA añadió 4 CVEs al KEV el 18/11 (CVE-2024-10441, CVE-2026-40217, CVE-2026-90436, CVE-2026-90817). NGINX no está en KEV aún.
- NVD publicó CVEs de NGINX el 18/11 con PoC público; explotación confirmada en investigación pero no en KEV.
- EPSS no disponible para los CVEs del 18/11 en las fuentes consultadas.
- Cobertura centrada en KEV y CVEs críticos con PoC público. No se detectaron otras adiciones oficiales al KEV el 18/11.
- CISA no publicó IOCs para los 4 CVEs añadidos al KEV el 18/11.

```json
{"fecha":"2026-11-18","cves":[{"id":"CVE-2024-10441","producto":"Synology DiskStation Manager (DSM)","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar DSM a version fija de la linea correspondiente (7.2.2-72806-1, 7.2.1-69057-6, 7.2-64570-4, 7.1.1-42962-7).","runbook":"1) Inventariar NAS Synology y version DSM. 2) Parchear a version fija. 3) Si no es posible, aislar del Internet. 4) Buscar indicadores de C3RB3R (.c3rb3r, ransom notes).","epss":null,"iocs":["archivos .c3rb3r","ransom note C3RB3R","procesos inusuales en NAS"],"sigma":"Regla Sigma: deteccion de creacion de archivos con extension .c3rb3r en Synology DSM.","playbook":{"triage":["Verificar version DSM instalada.","Buscar archivos .c3rb3r o ransom notes.","Revisar logs de acceso al NAS."],"contencion":["Aislar NAS de la red.","Deshabilitar acceso remoto.","Verificar integridad de backups offline."],"verificacion":["Confirmar version DSM parcheada.","Validar ausencia de archivos cifrados.","Restaurar desde backup limpio si es necesario."]}},"id":"CVE-2026-90817","producto":"Vanderbilt University REDCap","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a REDCap 16.0.49 / 17.3.10 / 17.4.4.","runbook":"1) Inventariar instancias REDCap >=13.3.0. 2) Actualizar a version fija. 3) Revisar logs de acceso a __passthru y Data Import. 4) Buscar procesos hijo inesperados del servidor web.","epss":null,"iocs":["requests HTTP anomalos a __passthru con file-path manipulado","procesos hijo inesperados del servidor web"],"sigma":"Regla Sigma: deteccion de requests HTTP a endpoint __passthru de REDCap con parametros de file-path sospechosos.","playbook":{"triage":["Verificar version REDCap instalada.","Revisar logs de acceso a __passthru y Data Import.","Buscar procesos hijo inesperados del servidor web."],"contencion":["Actualizar REDCap a version fija.","Restringir acceso a encuestas publicas si no son necesarias.","Segmentar el servidor REDCap."],"verificacion":["Confirmar version parcheada.","Validar ausencia de actividad anomala en logs post-parche."]}},"id":"CVE-2026-90436","producto":"Apache Axis 1.x","cvss":10.0,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Eliminar Axis 1.x del classpath (EOL, sin parche). N-able deshabilito Legacy API.","runbook":"1) Buscar JARs de org.apache.axis:axis en classpaths. 2) Eliminar o reemplazar Axis 1.x. 3) Revisar logs SOAP en Confluence/N-central. 4) Buscar procesos hijo inesperados de la JVM.","epss":null,"iocs":["requests SOAP con exceptionName sospechoso","procesos hijo inesperados de la JVM"],"sigma":"Regla Sigma: deteccion de requests SOAP con elemento exceptionName hacia Axis 1.x.","playbook":{"triage":["Buscar JARs de org.apache.axis:axis en classpaths.","Revisar logs de acceso SOAP en Confluence/N-central.","Buscar procesos hijo inesperados de la JVM."],"contencion":["Eliminar Axis 1.x del classpath.","Deshabilitar Legacy API en N-central.","Segmentar servidores afectados."],"verificacion":["Confirmar ausencia de JARs de Axis 1.x.","Validar que no haya actividad SOAP anomala post-mitigacion."]}},"id":"CVE-2026-40217","producto":"BerriAI LiteLLM","cvss":8.8,"severidad":"Alta","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar LiteLLM a version posterior a 2026-04-08; restringir /guardrails/test_custom_code.","runbook":"1) Inventariar instancias LiteLLM. 2) Actualizar a version posterior a 2026-04-08. 3) Restringir acceso a /guardrails/test_custom_code. 4) Revisar logs de acceso al endpoint.","epss":null,"iocs":["requests POST a /guardrails/test_custom_code","actividad anomala en contenedor LiteLLM","procesos hijo inesperados"],"sigma":"Regla Sigma: deteccion de requests POST a endpoint /guardrails/test_custom_code en LiteLLM.","playbook":{"triage":["Verificar version LiteLLM.","Revisar logs de acceso a /guardrails/test_custom_code.","Buscar procesos hijo inesperados en el contenedor."],"contencion":["Actualizar LiteLLM.","Restringir endpoint.","Rotar claves de API de LLM si se sospecha compromiso."],"verificacion":["Confirmar version parcheada.","Validar ausencia de actividad anomala post-parche.","Auditar claves de API."]}},"id":"CVE-2026-123007","producto":"NGINX (HTTP/2)","cvss":9.2,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"PoC","parche":"Actualizar a NGINX 1.31.9 (mainline) o 1.30.7 (stable); mitigacion temporal: deshabilitar HTTP/2.","runbook":"1) Inventariar servidores NGINX con HTTP/2 habilitado. 2) Verificar version instalada. 3) Parchear o deshabilitar HTTP/2. 4) Implementar IPS con firmas CVE-2026-123007.","epss":null,"iocs":["trafico HTTP/2 con volumen anomalo de control frames (WINDOW_UPDATE, RST_STREAM)","conexiones HTTP/2 de larga duracion con alto volumen de datos"],"sigma":"Regla Sigma: deteccion de trafico HTTP/2 con alto volumen de control frames indicativo de intento de explotacion CVE-2026-123007.","playbook":{"triage":["Inventariar servidores NGINX con HTTP/2 habilitado.","Verificar version instalada.","Revisar logs de acceso HTTP/2."],"contencion":["Parchear a 1.31.9/1.30.7.","Deshabilitar HTTP/2 como mitigacion temporal.","Implementar IPS con firmas CVE-2026-123007."],"verificacion":["Confirmar version parcheada.","Validar que HTTP/2 este deshabilitado si no se parcheo.","Monitorear trafico HTTP/2 post-mitigacion."]}}],"otras_amenazas":[{"nombre":"C3RB3R","tipo":"ransomware","resumen":"Ransomware desplegandose contra NAS Synology sin parchear con CVE-2024-10441."},{"nombre":"nginx2root","tipo":"campana","resumen":"5 CVEs de NGINX con PoC publico y explotacion confirmada en cloud (AWS, GCP, Azure)."}]}
```

---

# 📋 Informe CTI Diario — 2026-11-17

**Fecha del informe:** 18/11/2026  **Cobertura:** novedades del lunes 17 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) · 🟠 Alta (7-8.9) · 🟡 Media (4-6.9) · 🟢 Baja (<4).

> 💡 **Resumen en 30 segundos:** CISA añadió un nuevo CVE al KEV el 17/11: CVE-2026-115731 (Perforce Puppet Enterprise, deserialización, CVSS 4.0 9.4), con fecha federal de remediación el 01/12. Explotación confirmada; parche disponible desde el 03/11 (PE 2026.0.1/2025.12.1/2023.8.13 y Puppet Core 9.1.3/8.22.2). Revisar autosigning de certificados y certificados emitidos. Sin otras adiciones al KEV ese día.

---

## 1. Turno de vigilancia — qué atender primero

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 — Perforce Puppet Enterprise (CVE-2026-115731, 9.4, KEV) | Parchear a PE 2026.0.1 / 2025.12.1 / 2023.8.13 (o Puppet Core 9.1.3/8.22.2) y reiniciar Puppet Server; revisar autosigning y certificados emitidos. Deadline federal 01/12. |
| 2 | 🔴 P2 — Check Point Gaia / UnrealIRCd | Vencimiento federal 16/11 ya cumplido; si algún gateway/servidor sigue sin parchear, asumir compromiso y cazar IOCs. |
| 3 | 🟠 P3 — Dispositivos EOL (D-Link, DrayTek) | CVEs críticos 9.9-10.0 sin parche confirmado; aislar o reemplazar. |
| 4 | 🟡 General | NVD sin novedades críticas nuevas el 17/11; seguimiento de hallazgos previos. |

> ℹ️ **CISA añadió 1 CVE al KEV el 17/11.** Catálogo ahora incluye CVE-2026-115731 (Perforce Puppet Enterprise). Fecha límite federal: 01/12/2026.

---

## 2. Recomendaciones SOC L1

1. 🔴 **Prioridad 1 — Puppet Enterprise:** inventariar servidores Puppet (versión instalada); parchear a la versión correspondiente de la línea y reiniciar Puppet Server. Revisar autosigning (`puppet config print autosign --section server`) y deshabilitarlo o restringirlo a validación fuerte. Listar certificados firmados (`puppetserver ca list --all`) y revocar los no reconocidos.
2. 🔴 **Prioridad 2 — Check Point/UnrealIRCd:** confirmar que no quedan gateways/servidores sin parchear tras el deadline del 16/11; si los hay, asumir compromiso y cazar IOCs (webshells TSGA/TSProxy, `/tmp/dynamic_socket`, `destroy_v6`).
3. 🟠 **Prioridad 3 — Dispositivos EOL:** aislar del Internet o reemplazar D-Link DSL-6740C/DIR-600L y DrayTek Vigor2960 con CVEs críticos sin parche.
4. 🟡 **General:** revisar reportes y actividad del servidor Puppet desde el 03/11 en busca de abuso (submissions desde hosts inusuales, procesos hijo inesperados del servicio Puppet).

## 3. Recomendaciones SOC L2

1. 🟠 **Hardening:** deshabilitar autosigning abierto en Puppet; restringir emisión de certificados a aprobación manual o validación fuerte; segmentar el Puppet Server del resto de la red.
2. 🟠 **Threat hunting:** buscar certificados emitidos a nombres no reconocidos alrededor del 03/11; revisar logs de reportes del Puppet Server; cazar actividad de wfx-loot en repositorios GitHub.
3. 🟡 **Tuning de detecciones:** añadir reglas Sigma para submissions de reportes Puppet anómalos y procesos hijo inesperados del servicio Puppet Server.
4. 🟡 **Automatización:** integrar el catálogo KEV en el pipeline de priorización; alertar automáticamente ante nuevas adiciones con deadline federal.
5. 🟢 **Mejoras de proceso:** documentar estado de remediación por CVE; usar fechas KEV (BOD 26-04) como SLA interno de parcheo.

---

## 4. Resumen ejecutivo

- **Nuevo KEV el 17/11:** CVE-2026-115731 (Perforce Puppet Enterprise, deserialización de datos no confiables, CVSS 4.0 9.4). Explotación confirmada por CISA; deadline federal 01/12/2026.
- **Parche disponible desde el 03/11:** PE 2026.0.1 / 2025.12.1 / 2023.8.13 y Puppet Core 9.1.3 / 8.22.2. Requiere reinicio del Puppet Server.
- **Riesgo principal:** un cliente con certificado firmado por la CA puede enviar un reporte manipulado y ejecutar código en el Puppet Server; con autosigning abierto, la barrera de entrada cae a casi cero.
- **CVE hermano CVE-2026-115732** (bypass de autorización, CVSS ~9.0) se corrige en las mismas versiones; parchear una corrige ambas.
- **Check Point Gaia y UnrealIRCd:** deadline del 16/11 cumplido; verificar que no queden sistemas sin parchear.

---

## 5. Novedades del 17/11

### 5.1 KEV / explotación confirmada

| CVE | CVSS | Producto | Impacto | EPSS |
|---|---|---|---|---|
| 🔴 CVE-2026-115731 | 9.4 (CVSS 4.0) | Perforce Puppet Enterprise | Deserialización de datos no confiables en el procesamiento de reportes → RCE como usuario del Puppet Server | — |

> ℹ️ CISA no nombró atacante ni publicó IOCs. El listing confirma explotación activa; el alcance real se desconoce.

### 5.2 Hallazgo por CVE (detalle)

**CVE-2026-115731 — Perforce Puppet Enterprise (deserialización de datos no confiables)**

- **CVSS:** 9.4 (Crítica, CVSS 4.0). Algunas bases muestran 9.9 en CVSS 3.x; el registro oficial lista 9.4 en CVSS 4.0. **CWE-502** (deserialización de datos no confiables).
- **Producto afectado:** Puppet Enterprise 2023.8.0–2023.8.12, 2025.0.0–2025.12.0, 2026.0.0 (corregido en 2023.8.13 / 2025.12.1 / 2026.0.1). Puppet Core 8.22.1 y anteriores y 9.1.2 y anteriores (corregido en 8.22.2 / 9.1.3).
- **Explotación:** activa (KEV el 17/11). Requiere certificado de agente firmado por la CA. Con autosigning abierto, la barrera cae a casi cero.
- **Impacto:** RCE como usuario del servicio Puppet Server, con acceso potencial a la clave de firma de certificados y a la compilación de catálogos del fleet.
- **Mitigación:** actualizar a la versión fija de la línea correspondiente y reiniciar Puppet Server; deshabilitar o restringir autosigning; auditar certificados firmados; revisar reportes y actividad desde el 03/11.
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application) y T1078 (Valid Accounts) por el uso de certificados de agente.

**CVE-2026-115732 — Puppet Enterprise (bypass de autorización, hermano)**

- **CVSS:** ~9.0 (Alta, CVSS 3.x, cifra de fuente única). **CWE** no confirmado en fuentes.
- **Producto afectado:** mismas líneas que CVE-2026-115731.
- **Explotación:** sin evidencia de explotación (no figura en KEV).
- **Mitigación:** se corrige en las mismas versiones; parchear una corrige ambas.

---

## 6. Contexto de seguimiento (sin cambios respecto al 16/11)

- **CVE-2026-123039 (Check Point Gaia)** y **CVE-2026-114713 (UnrealIRCd):** deadline federal 16/11 cumplido. Sin novedades adicionales. Sistemas sin parchear deben asumirse comprometidos.
- **CVE-2026-124194 (Outlook, NTLMv2 leak):** sin cambios; controles de bloqueo NTLM saliente vigentes.
- **PHP2Shell (CVE-2026-109618/109619):** explotación activa contra portales Mobile Access y PAN-OS; sin novedades el 17/11.
- **Dispositivos EOL (D-Link, DrayTek):** sin cambios; pendiente de reemplazo o segmentación.

---

## 7. Cobertura y limitaciones

- CISA añadió un único CVE al KEV el 17/11 (CVE-2026-115731). No se detectaron otras adiciones oficiales.
- NVD sin novedades críticas adicionales publicadas el 17/11; cobertura centrada en el KEV y seguimiento de hallazgos previos.
- EPSS y estados de explotación corresponden a las fuentes citadas a la fecha de consulta (18/11/2026). CISA no publicó IOCs para CVE-2026-115731.

```json
{"fecha":"2026-11-17","cves":[{"id":"CVE-2026-115731","producto":"Perforce Puppet Enterprise (Puppet Server)","cvss":9.4,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV","parche":"Actualizar a PE 2026.0.1 / 2025.12.1 / 2023.8.13 o Puppet Core 9.1.3 / 8.22.2 y reiniciar Puppet Server.","runbook":"1) Inventariar servidores Puppet y version instalada. 2) Parchear a la version fija de la linea correspondiente. 3) Reiniciar Puppet Server. 4) Revisar autosigning y certificados firmados; revocar los no reconocidos.","epss":null,"iocs":[],"sigma":"Regla Sigma: deteccion de submissions de reportes Puppet anormales hacia el Puppet Server desde hosts no reconocidos.","playbook":{"triage":["Verificar version instalada de Puppet Server.","Revisar configuracion de autosigning."],"contencion":["Deshabilitar o restringir autosigning.","Segmentar el Puppet Server de la red."],"verificacion":["Confirmar version parcheada tras reinicio.","Validar ausencia de certificados no reconocidos y de actividad anomala desde el 03/11."]}}],"otras_amenazas":[]}
```
ENTRYEOF
echo done

# 📋 Informe CTI Diario — 2026-11-16

**Fecha del informe:** 17/11/2026  **Cobertura:** novedades del domingo 16 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) · 🟠 Alta (7-8.9) · 🟡 Media (4-6.9) · 🟢 Baja (<4).

> 💡 **Resumen en 30 segundos:** Sin nuevas adiciones oficiales al KEV el 16/11 (última: 13/11). Hoy venció la fecha federal para Check Point Gaia y UnrealIRCd: verificar parcheo antes de cerrar el turno. NVD publicó ~200 CVEs en 30 h, 32 de score ≥8.5; destacan Dassault 3DEXPERIENCE (CVSS 10), D-Link DSL-6740C (10, creds hard-coded, PoC público), DrayTek Vigor2960 (9.9, fix 1.5.1.7), ConnectWise ScreenConnect (9.9, marcado KEV por terceros) y Apache Axis 1.x/N-able (9.8, explotado). Prioridad: confirmar que Gaia/UnrealIRCd quedaron parcheados; si no, asumir compromiso y cazar IOCs.

---

## 1. Turno de vigilancia — qué atender primero

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 — Check Point Gaia / UnrealIRCd | Vencimiento federal **16/11**. Verificar parche aplicado; si no, asumir compromiso y cazar IOCs (webshells TSGA/TSProxy, `/tmp/dynamic_socket`, `destroy_v6`). |
| 2 | 🔴 P2 — ConnectWise ScreenConnect (CVE-2026-84869, 9.9) | Reportado como KEV el 16/11 por fuentes de terceros. Verificar versión del servidor y parchear de inmediato. |
| 3 | 🔴 P3 — D-Link DSL-6740C (CVE-2026-129444, 10.0) | Creds hard-coded en FTP, PoC público, producto EOL. Aislar del Internet o reemplazar. |
| 4 | 🟠 P4 — DrayTek Vigor2960 (CVE-2026-129449, 9.9) | Heap overflow con PoC público; actualizar a 1.5.1.7. |
| 5 | 🟠 P5 — N-able N-central / Apache Axis 1.x (CVE-2026-90436, 9.8) | Explotación confirmada. Aplicar HF3 (2026.4.1.39) y actualizar agentes/probes. |
| 6 | 🟡 General | Dassault 3DEXPERIENCE (CVE-2026-93664, 10.0) y Prime CMS/WordPress OTP (9.8): inventario y parcheo rutinario. |

> ℹ️ **Sin nuevas adiciones oficiales al KEV el 16/11.** Catálogo estable en versión 2026.11.13 (últimas adiciones 13/11). Reportes de terceros (Tuskira) marcan CVE-2026-84869 (ConnectWise ScreenConnect) y CVE-2026-111560 (Barracuda) como KEV con fecha 16/11; verificar en el catálogo CISA antes de tratarlos como obligatorios.

---

## 2. Recomendaciones SOC L1 y Recomendaciones SOC L2

### Recomendaciones SOC L1

1. 🔴 **Prioridad 1 — Check Point/UnrealIRCd:** confirmar estado de parche en gateways Gaia y servidores IRC tras el vencimiento federal del 16/11; si no parcheados, asumir compromiso y cazar IOCs (webshells TSGA/TSProxy, `/tmp/dynamic_socket`, `destroy_v6`, movimiento SSH a ESXi).
2. 🔴 **Prioridad 2 — ConnectWise ScreenConnect:** inventariar servidores ScreenConnect; aplicar la versión parcheada; revisar logs de acceso remoto y sesiones no autorizadas.
3. 🔴 **Prioridad 3 — D-Link DSL-6740C:** aislar del Internet o reemplazar (EOL, sin parche); bloquear FTP (puerto 21) en perímetro.
4. 🟠 **Prioridad 4 — DrayTek Vigor2960:** actualizar a 1.5.1.7; monitorear requests a `mainfunction.cgi` / `check_file_exist`.
5. 🟠 **Prioridad 5 — N-able N-central:** aplicar HF3 (2026.4.1.39), actualizar agentes y probes; revisar uso de Legacy API.
6. 🟡 **General:** inventariar 3DEXPERIENCE (Station Launcher), WordPress (OTP plugin) y Prime CMS; aplicar parches cuando el fabricante los publique.

### Recomendaciones SOC L2

1. 🟠 **Hardening:** deshabilitar FTP en dispositivos EOL; restringir ScreenConnect a segmentos confiables; revisar configuración de Schannel/TLS en listeners.
2. 🟠 **Threat hunting:** buscar actividad de wfx-loot en repositorios GitHub (campaña reportada); revisar logs de NetScaler/Citrix (KEV pendientes); cazar IOCs de ScreenConnect (sesiones anómalas, nuevos usuarios admin).
3. 🟡 **Tuning de detecciones:** añadir reglas Sigma para webshells PHP (TSGA/TSProxy), comandos DynamicCLI sin wrapper Clish, y requests a `mainfunction.cgi` / `check_file_exist` en DrayTek.
4. 🟡 **Automatización:** integrar EPSS en el pipeline de priorización; exportar catálogo CVE a SIEM para correlación con inventario de activos.
5. 🟢 **Mejoras de proceso:** definir SLA interno de parcheo alineado a fechas KEV (BOD 26-04); documentar estado de remediación por CVE; verificar parcheo post-deadline federal.

---

## 3. Resumen ejecutivo

- **Deadline federal 16/11 para Check Point Gaia y UnrealIRCd:** verificar hoy que ambos quedaron parcheados; si no, asumir compromiso y cazar IOCs.
- **KEV sin cambios oficiales el 16/11:** catálogo estable en 2026.11.13. Reportes de terceros marcan ConnectWise ScreenConnect (CVE-2026-84869) y Barracuda (CVE-2026-111560) como KEV con fecha 16/11; verificar en CISA.
- **NVD publicó ~200 CVEs en 30 h:** 32 de score ≥8.5. Destacan Dassault 3DEXPERIENCE (10.0, deserialización), D-Link DSL-6740C (10.0, creds hard-coded, PoC público) y DrayTek Vigor2960 (9.9, heap overflow, PoC público).
- **Explotación confirmada:** Apache Axis 1.x / N-able N-central (CVE-2026-90436, 9.8) con fix en HF3; ConnectWise ScreenConnect (9.9) marcado KEV por terceros.
- **Dispositivos EOL críticos:** D-Link DSL-6740C sin parche confirmado; planificar reemplazo o segmentación urgente.

---

## 4. Novedades del 16/11

### 4.1 KEV / explotación confirmada

| CVE | CVSS | Producto | Impacto | EPSS |
|---|---|---|---|---|
| 🔴 CVE-2026-84869 | 9.9 | ConnectWise ScreenConnect | RCE sin autenticación (marcado KEV por terceros el 16/11) | — |
| 🔴 CVE-2026-90436 | 9.8 | Apache Axis 1.x / N-able N-central | RCE sin autenticación, explotado en la calle | — |
| 🟠 CVE-2026-111560 | 7.2 | Barracuda (OS command injection) | KEV (reportado por terceros) | — |

> ℹ️ Verificar en el catálogo CISA antes de tratar ScreenConnect/Barracuda como KEV obligatorio. Check Point Gaia (CVE-2026-123039) y UnrealIRCd (CVE-2026-114713) siguen en KEV con vencimiento cumplido el 16/11.

### 4.2 CVEs críticos publicados el 16/11 (sin KEV confirmado)

| CVE | CVSS | Producto | Impacto |
|---|---|---|---|
| 🔴 CVE-2026-93664 | 10.0 | Dassault Systèmes 3DEXPERIENCE (Station Launcher App) | Deserialización no confiable → RCE sin autenticación |
| 🔴 CVE-2026-129444 | 10.0 | D-Link DSL-6740C | Creds hard-coded en FTP, PoC público, producto EOL |
| 🔴 CVE-2026-129449 | 9.9 | DrayTek Vigor2960 | Heap overflow en mainfunction.cgi, PoC público, fix 1.5.1.7 |
| 🔴 CVE-2026-8760 | 9.8 | Plugin WordPress OTP | Authentication bypass |
| 🔴 CVE-2026-129941 | 9.8 | Prime CMS | Authentication bypass |
| 🟠 CVE-2026-8775 | 8.8 | Edimax BR-6428NS | Buffer overflow |
| 🟠 CVE-2026-110697 | 9.9 | Red Hat Quay | Robo de robot tokens (self-hosted) |

### 4.3 Hallazgos por CVE (detalle)

**CVE-2026-93664 — Dassault Systèmes 3DEXPERIENCE (Station Launcher App)**
- **CVSS:** 10.0 (Crítica) — **CWE-502** (deserialización de datos no confiables).
- **Producto afectado:** Station Launcher App en plataforma 3DEXPERIENCE (versiones no especificadas por el fabricante).
- **Explotación:** sin explotación confirmada a la fecha; sin PoC público.
- **Mitigación:** consultar al fabricante/IT provider la versión instalada y disponibilidad de fix; no hay versión parcheada confirmada.

**CVE-2026-129444 — D-Link DSL-6740C**
- **CVSS:** 10.0 (Crítica) — **CWE-798 / CWE-259** (credenciales hard-coded).
- **Producto afectado:** D-Link DSL-6740C firmware 20131126 (EOL).
- **Explotación:** PoC público disponible; sin explotación en KEV confirmada.
- **Mitigación:** no hay firmware parcheado confirmado; aislar FTP (puerto 21) del Internet o reemplazar el dispositivo.

**CVE-2026-129449 — DrayTek Vigor2960**
- **CVSS:** 9.9 (Crítica) — heap-based buffer overflow en `check_file_exist` de `mainfunction.cgi`.
- **Producto afectado:** DrayTek Vigor2960 1.5.1.6 (y versiones anteriores).
- **Explotación:** PoC público disponible; sin explotación en KEV confirmada.
- **Mitigación:** actualizar a 1.5.1.7; monitorear requests a `mainfunction.cgi`.

**CVE-2026-84869 — ConnectWise ScreenConnect**
- **CVSS:** 9.9 (Crítica).
- **Producto afectado:** ConnectWise ScreenConnect (technician client / servidor de acceso remoto).
- **Explotación:** marcado como KEV por fuentes de terceros (Tuskira) con fecha 16/11; verificar en CISA.
- **Mitigación:** aplicar la versión parcheada del fabricante; revisar sesiones remotas y cuentas admin.

**CVE-2026-90436 — Apache Axis 1.x / N-able N-central**
- **CVSS:** 9.8 (Crítica) — Apache Axis 1.x EOL, incluido embebido en otras apps Java.
- **Producto afectado:** N-able N-central (on-prem y hosted), Apache Axis 1.x.
- **Explotación:** explotación confirmada (Rapid7).
- **Mitigación:** aplicar HF3 (2026.4.1.39) que bloquea la Legacy API; actualizar todos los agentes y probes.

---

## 5. Contexto de seguimiento (sin cambios respecto al 15/11)

- **CVE-2026-123039 (Check Point Gaia)** y **CVE-2026-114713 (UnrealIRCd):** vencimiento federal cumplido el 16/11. Sin novedades adicionales. Un gateway/servidor sin parchear debe asumirse comprometido.
- **CVE-2026-124194 (Outlook, NTLMv2 leak):** sin cambios; controles de bloqueo NTLM saliente vigentes.
- **PHP2Shell (CVE-2026-109618/109619):** explotación activa contra portales Mobile Access y PAN-OS; sin novedades el 16/11.
- **Siemens Desigo / Node2Shell (CVE-2026-111497):** advisory ICSA-26-316-02 del 12/11, pendiente de análisis detallado.

---

## 6. Cobertura y limitaciones

- No se detectaron nuevas adiciones oficiales al KEV el 16/11; cobertura centrada en NVD (~200 CVEs en 30 h) y seguimiento de hallazgos previos.
- ScreenConnect (CVE-2026-84869) y Barracuda (CVE-2026-111560) figuran como KEV solo en fuentes de terceros (Tuskira); verificar en catálogo CISA.
- EPSS y estados de explotación corresponden a las fuentes citadas a la fecha de consulta (17/11/2026).

```json
{"fecha":"2026-11-16","cves":[{"id":"CVE-2026-93664","producto":"Dassault Systemes 3DEXPERIENCE Station Launcher App","cvss":10.0,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Sin version parcheada confirmada; consultar al fabricante.","runbook":"1) Identificar instancias 3DEXPERIENCE con Station Launcher. 2) Consultar al fabricante version y fix. 3) Restringir acceso al launcher. 4) Monitorear deserializacion anormal.","epss":null,"iocs":[],"sigma":"Regla Sigma: deteccion de deserializacion Java anormal en Station Launcher.","playbook":{"triage":["Verificar version instalada.","Revisar logs de deserializacion."],"contencion":["Restringir acceso al launcher.","Segmentar la plataforma."],"verificacion":["Confirmar version parcheada.","Validar ausencia de actividad anomala."]}},{"id":"CVE-2026-129444","producto":"D-Link DSL-6740C","cvss":10.0,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"PoC","parche":"Producto EOL; sin parche confirmado; aislar FTP o reemplazar.","runbook":"1) Inventariar dispositivos DSL-6740C expuestos. 2) Bloquear FTP (puerto 21) en perimetro. 3) Planificar reemplazo. 4) Monitorear trafico FTP.","epss":null,"iocs":[],"sigma":"Regla Sigma: deteccion de conexiones FTP a dispositivos D-Link DSL-6740C.","playbook":{"triage":["Identificar dispositivos expuestos.","Verificar firmware 20131126."],"contencion":["Bloquear FTP en firewall.","Aislar del Internet."],"verificacion":["Confirmar ausencia de trafico FTP externo.","Documentar plan de reemplazo."]}},{"id":"CVE-2026-129449","producto":"DrayTek Vigor2960","cvss":9.9,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"PoC","parche":"Actualizar a 1.5.1.7.","runbook":"1) Identificar instancias Vigor2960. 2) Actualizar a 1.5.1.7. 3) Monitorear requests a mainfunction.cgi. 4) Revisar logs de check_file_exist.","epss":null,"iocs":[],"sigma":"Regla Sigma: deteccion de requests HTTP a mainfunction.cgi con check_file_exist en DrayTek.","playbook":{"triage":["Verificar version instalada.","Revisar logs HTTP."],"contencion":["Aplicar parche 1.5.1.7.","Bloquear acceso externo a CGI."],"verificacion":["Confirmar version >= 1.5.1.7.","Validar ausencia de requests sospechosos."]}},{"id":"CVE-2026-84869","producto":"ConnectWise ScreenConnect","cvss":9.9,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"KEV (reportado por terceros)","parche":"Aplicar version parcheada del fabricante.","runbook":"1) Inventariar servidores ScreenConnect. 2) Aplicar parche. 3) Revisar sesiones remotas y cuentas admin. 4) Monitorear accesos anormales.","epss":null,"iocs":[],"sigma":"Regla Sigma: deteccion de sesiones remotas anormales en ScreenConnect.","playbook":{"triage":["Verificar version del servidor.","Revisar cuentas admin."],"contencion":["Aplicar parche.","Restringir a segmentos confiables."],"verificacion":["Confirmar version parcheada.","Validar ausencia de sesiones no autorizadas."]}},{"id":"CVE-2026-90436","producto":"Apache Axis 1.x / N-able N-central","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"explotada en la calle","parche":"Aplicar HF3 (2026.4.1.39) y actualizar agentes/probes.","runbook":"1) Identificar instancias N-central. 2) Aplicar HF3. 3) Actualizar agentes y probes. 4) Deshabilitar Legacy API.","epss":null,"iocs":[],"sigma":"Regla Sigma: deteccion de uso de Legacy API en N-able N-central.","playbook":{"triage":["Verificar version de N-central.","Revisar uso de Legacy API."],"contencion":["Aplicar HF3.","Deshabilitar Legacy API."],"verificacion":["Confirmar HF3 aplicado.","Validar ausencia de explotacion."]}},{"id":"CVE-2026-8760","producto":"Plugin WordPress OTP","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar o desactivar el plugin.","runbook":"1) Inventariar sitios WordPress con el plugin OTP. 2) Actualizar o desactivar. 3) Revisar cuentas creadas recientemente. 4) Auditar logs de autenticacion.","epss":null,"iocs":[],"sigma":"Regla Sigma: deteccion de bypass de autenticacion OTP en WordPress.","playbook":{"triage":["Identificar sitios afectados.","Verificar version del plugin."],"contencion":["Actualizar o desactivar.","Revisar cuentas admin."],"verificacion":["Confirmar version parcheada.","Validar ausencia de cuentas no autorizadas."]}}],"otras_amenazas":[{"nombre":"Check Point Gaia / UnrealIRCd (deadline federal 16/11)","tipo":"campana","resumen":"Vencimiento federal cumplido el 16/11; verificar parcheo y asumir compromiso si no parcheado."}]}
```

---


**Fecha del informe:** 16/11/2026  **Cobertura:** novedades del sábado 15 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Leyenda de severidad:** 🔴 Crítica (9-10) · 🟠 Alta (7-8.9) · 🟡 Media (4-6.9) · 🟢 Baja (<4).

> 💡 **Resumen en 30 segundos:** Sin nuevas adiciones al KEV (última: 13/11). NVD publicó 112 CVEs el 15/11, 15 críticos, ninguno explotado. Prioridad real sigue siendo Check Point Gaia + UnrealIRCd (vencimiento federal **16/11**). Acción inmediata: verificar parcheo antes de esa fecha.

---

## 1. Turno de vigilancia — qué atender primero

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 - Check Point Gaia / UnrealIRCd | Vencimiento federal **16/11**. Verificar parche aplicado; si no, asumir compromiso y cazar IOCs. |
| 2 | 🟠 P2 - Windows Patch Tuesday (LDAP/Schannel/CredSSP/IIS/MSMQ) | CVE-2026-119958 LDAP (9.8) y otros RCEs sin KEV pero red-reachables. Parchear DCs y servidores expuestos en este ciclo. |
| 3 | 🟠 P3 - Dispositivos EOL (D-Link, DrayTek) | CVEs críticos 9.9-10.0 en routers EOL/sin soporte. Inventariar y reemplazar o segmentar. |
| 4 | 🟡 P4 - WordPress plugins críticos | Object injection/RCE en plugins (Auth Armor, Lazy Blocks, Document Library Pro, etc.). Inventariar sitios. |
| 5 | 🟢 General | KEV estable desde 13/11; próxima revisión de catálogo el lunes. |

> ℹ️ **Sin nuevas adiciones al KEV el 15/11.** Catálogo sigue en versión 2026.11.13. Últimas adiciones: UnrealIRCd (CVE-2026-114713) y Check Point Gaia (CVE-2026-123039), vencimiento 16/11.

---

## 2. Recomendaciones SOC L1

1. 🔴 **Prioridad 1 - Check Point/UnrealIRCd:** verificar estado de parche en gateways Gaia y servidores IRC antes del 16/11; si no parcheados, cazar IOCs (webshells TSGA/TSProxy, `/tmp/dynamic_socket`, `destroy_v6`).
2. 🟠 **Prioridad 2 - Windows:** parchear controladores de dominio (LDAP CVE-2026-119958) y servidores con Schannel/CredSSP/IIS/MSMQ en este ciclo; mantener LDAP/LDAPS fuera de Internet.
3. 🟠 **Prioridad 3 - Dispositivos de borde EOL:** segmentar o reemplazar D-Link DIR-600L/DSL-6740C y DrayTek Vigor2960 con CVEs críticos sin parche disponible.
4. 🟡 **Prioridad 4 - WordPress:** inventariar sitios con plugins críticos (Auth Armor, Lazy Blocks, Document Library Pro, Advanced Form Integration, Estatik) y actualizar o desactivar.
5. 🟢 **General:** monitorear KEV el lunes 17/11; revisar que los update rings de Microsoft 365 Apps se hayan movido (cubre CVE-2026-124194 + heap overflows).

## Recomendaciones SOC L2

1. 🟠 **Hardening:** deshabilitar MSMQ donde no se use (TCP 1801); restringir LDAP/LDAPS a segmentos confiables; revisar configuración de Schannel en listeners TLS.
2. 🟠 **Threat hunting:** buscar actividad de wfx-loot en repositorios GitHub (campaña reportada el 15/11); revisar logs de NetScaler si aplica (Citrix KEV pendientes).
3. 🟡 **Tuning de detecciones:** añadir reglas Sigma para webshells PHP (TSGA/TSProxy) y comandos DynamicCLI sin wrapper Clish; alertar sobre NTLMv2 hacia externos.
4. 🟡 **Automatización:** integrar EPSS en el pipeline de priorización; exportar catálogo CVE a SIEM para correlación con inventario de activos.
5. 🟢 **Mejoras de proceso:** definir SLA interno de parcheo alineado a fechas KEV (BOD 26-04); documentar estado de remediación por CVE.

---

## 3. Resumen ejecutivo

- **KEV sin cambios el 15/11:** no se detectaron nuevas adiciones; catálogo estable en versión 2026.11.13. Check Point Gaia y UnrealIRCd siguen siendo prioridad 1 con vencimiento 16/11.
- **NVD publicó 112 CVEs el 15/11:** 15 críticos y 39 altos, ninguno en KEV. Destacan RCEs sin autenticación en Windows (LDAP, Schannel, CredSSP, IIS, MSMQ) y dispositivos de borde EOL (D-Link, DrayTek).
- **Windows Patch Tuesday reciente:** CVE-2026-119958 (LDAP, 9.8, EPSS 1.3%) es el más urgente por exposición de controladores de dominio; Schannel/CredSSP/IIS/MSMQ (8.1) en ciclo normal.
- **Dispositivos EOL críticos:** D-Link DIR-600L/DSL-6740C y DrayTek Vigor2960 con CVEs 9.9-10.0 sin parche confirmado; planificar reemplazo o segmentación.
- **WordPress:** múltiples plugins con RCE/object injection críticos; inventario y parcheo rutinario.

---

## 4. Novedades del 15/11 (sin explotación confirmada)

### 4.1 CVEs críticos de Windows (Patch Tuesday)

| CVE | CVSS | Producto | Impacto | EPSS |
|---|---|---|---|---|
| 🟠 CVE-2026-119958 | 9.8 | Windows LDAP | RCE sin autenticación sobre DCs | 1.3% |
| 🟠 CVE-2026-119952 | 8.1 | Schannel (TLS) | RCE sin autenticación, alta complejidad | 0.9% |
| 🟠 CVE-2026-119962 | 8.1 | CredSSP (RDP) | Double free, alta complejidad | 0.9% |
| 🟠 CVE-2026-119961 | 8.1 | IIS | Use-after-free, race condition | 0.6% |
| 🟠 CVE-2026-119963 | 8.1 | Message Queuing | Double free sin autenticación (TCP 1801) | 0.6% |
| 🟡 CVE-2026-120003 | 8.8 | Office/Outlook | Heap overflow, requiere abrir archivo | 1.0% |
| 🟡 CVE-2026-110043 | 8.8 | Office/Outlook | Heap overflow, requiere abrir archivo | 1.1% |

> ℹ️ Ninguno figura en KEV. El parche de Microsoft 365 Apps cubre CVE-2026-124194 (Outlook NTLMv2 leak) + los dos heap overflows.

### 4.2 Dispositivos de borde EOL / críticos

| CVE | CVSS | Producto | Impacto |
|---|---|---|---|
| 🔴 CVE-2026-129353 | 10.0 | D-Link DIR-600L | Stack overflow en authentication.cgi |
| 🔴 CVE-2026-129354 | 10.0 | D-Link DIR-600L | Stack overflow en HNAP cgibin |
| 🔴 CVE-2026-129444 | 10.0 | D-Link DSL-6740C | Credenciales hard-coded en FTP daemon |
| 🔴 CVE-2026-129449/450/451/453 | 9.9 | DrayTek Vigor2960 | Command injection / path traversal en mainfunction.cgi |
| 🔴 CVE-2026-129349 | 9.1 | Apache Shiro | DefaultLdapRealm acepta cualquier password con caché de auth |
| 🔴 CVE-2026-129483 | 9.1 | Cyrus SASL | Bypass de autenticación SRP |
| 🔴 CVE-2026-110960 | 9.8 | Auth Armor <= 1.0.3 | Account takeover sin autenticación |
| 🔴 CVE-2026-127048 | 9.8 | Lazy Blocks < 3.0.0 | RCE sin autenticación |

### 4.3 WordPress plugins críticos

| CVE | CVSS | Plugin | Impacto |
|---|---|---|---|
| 🔴 CVE-2026-122662 | 9.8 | Document Library Pro | PHP Object Injection |
| 🔴 CVE-2026-125769 | 9.8 | Advanced Form Integration | Privilege Escalation |
| 🔴 CVE-2026-125795 | 9.8 | Estatik | Local File Inclusion |

> ℹ️ Más de 30 plugins WordPress con XSS/SQLi de severidad alta (7.1-8.8) publicados el 15/11; inventario rutinario.

---

## 5. Contexto de seguimiento (sin cambios respecto al 14/11)

- **CVE-2026-123039 (Check Point Gaia)** y **CVE-2026-114713 (UnrealIRCd):** siguen en KEV, vencimiento federal 16/11. Sin novedades adicionales el 15/11. Un gateway sin parchear el 17/11 debe asumirse comprometido.
- **CVE-2026-124194 (Outlook, NTLMv2 leak):** sin cambios; controles de bloqueo NTLM saliente vigentes.
- **PHP2Shell (CVE-2026-109618/109619):** explotación activa contra portales Mobile Access y PAN-OS; sin novedades el 15/11.
- **Siemens Desigo / Node2Shell (CVE-2026-111497):** advisory ICSA-26-316-02 del 12/11, pendiente de análisis detallado.

---

## 6. Cobertura y limitaciones

- No se detectaron nuevas adiciones al KEV el 15/11; cobertura centrada en NVD (112 CVEs publicados) y seguimiento de hallazgos previos.
- EPSS y estados de explotación corresponden a las fuentes citadas a la fecha de consulta (16/11/2026).
- No se analizaron individualmente los 39 CVEs de severidad alta de WordPress/XSS; se agruparon como rutinarios.

```json
{"fecha":"2026-11-15","cves":[{"id":"CVE-2026-119958","producto":"Windows LDAP","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"sin explotacion confirmada","parche":"Parche de noviembre (Patch Tuesday); actualizar DCs.","runbook":"1) Identificar controladores de dominio. 2) Aplicar parche de noviembre. 3) Mantener LDAP/LDAPS fuera de Internet. 4) Monitorear trafico LDAP anormal.","epss":0.013,"iocs":[],"sigma":"Regla Sigma: deteccion de trafico LDAP inusual hacia DCs desde segmentos no confiables.","playbook":{"triage":["Verificar version del DC y estado del parche.","Revisar logs de eventos LDAP (4662, 5136)."],"contencion":["Aislar DCs de segmentos no confiables.","Bloquear LDAP/LDAPS en firewall perimetral."],"verificacion":["Confirmar build >= version parcheada.","Validar ausencia de conexiones LDAP externas."]}},{"id":"CVE-2026-129353","producto":"D-Link DIR-600L","cvss":10.0,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"sin explotacion confirmada","parche":"Producto EOL; sin parche confirmado; planificar reemplazo o segmentacion.","runbook":"1) Inventariar dispositivos DIR-600L expuestos. 2) Segmentar o retirar de Internet. 3) Planificar reemplazo. 4) Monitorear trafico hacia el dispositivo.","epss":0.018,"iocs":[],"sigma":"Regla Sigma: deteccion de requests HTTP a /authentication.cgi o /HNAP en dispositivos D-Link.","playbook":{"triage":["Identificar dispositivos DIR-600L en inventario.","Verificar exposicion a Internet."],"contencion":["Desconectar de Internet o segmentar.","Bloquear puertos de gestion."],"verificacion":["Confirmar ausencia de trafico hacia el dispositivo desde externos.","Documentar plan de reemplazo."]}},{"id":"CVE-2026-129449","producto":"DrayTek Vigor2960","cvss":9.9,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"sin explotacion confirmada","parche":"Actualizar firmware segun aviso del fabricante.","runbook":"1) Inventariar Vigor2960. 2) Actualizar firmware. 3) Restringir acceso a mainfunction.cgi. 4) Revisar logs de acceso.","epss":null,"iocs":[],"sigma":"Regla Sigma: deteccion de requests a mainfunction.cgi con parametros de command injection o path traversal.","playbook":{"triage":["Identificar instancias Vigor2960 expuestas.","Revisar version de firmware."],"contencion":["Restringir acceso a la interfaz de gestion.","Aplicar firmware actualizado."],"verificacion":["Confirmar version de firmware actualizada.","Validar ausencia de requests maliciosos en logs."]}},{"id":"CVE-2026-123039","producto":"Check Point Gaia OS (DynamicCLI)","cvss":7.8,"severidad":"Alta","mitre":{"id":"T1068","nombre":"Exploitation for Privilege Escalation"},"explotacion":"KEV","parche":"Jumbo Hotfix R82.20 Take 14+, R82.10 Take 52+, R82 Take 132+, R81.20 Take 174+; verificar /tmp/dynamic_socket en 0660.","runbook":"1) Inventariar gateways Gaia y take del Jumbo. 2) Parchear o detener dynamic_server. 3) Cazar comandos en /var/log/dynamicCli.log sin wrapper Clish. 4) Buscar webshells TSGA/TSProxy y wiper destroy_v6 en ESXi.","epss":0.3,"iocs":["/tmp/dynamic_socket","130.94.19.204","webshell TSGA","webshell TSProxy","destroy_v6","DREAM_ANNEXATION.txt"],"sigma":"Regla YARA: deteccion de webshells PHP TSGA/TSProxy en plantillas de login del portal Check Point.","playbook":{"triage":["Verificar version y take del Jumbo en cada gateway.","Revisar /tmp/dynamic_socket permisos (debe ser 0660).","Buscar webshells en plantillas de login."],"contencion":["Aplicar Jumbo Hotfix o detener dynamic_server.","Bloquear conexiones desde 130.94.19.204.","Aislar gateways sospechosos."],"verificacion":["Confirmar socket en 0660.","Validar ausencia de comandos sin wrapper Clish en dynamicCli.log.","Verificar no haya movimiento SSH a ESXi ni wiper destroy_v6."]}}],"otras_amenazas":[{"nombre":"wfx-loot","tipo":"campana","resumen":"Campana dirigida a repositorios GitHub reportada el 15/11."}]}
```

# ️ Informe CTI Diario — 2026-11-14

**Fecha del informe:** 15/11/2026  **Cobertura:** novedades del sábado 14 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs y fabricantes oficiales.
**Leyenda de severidad:** 🔴 Crítica (9-10) · 🟠 Alta (7-8.9) · 🟡 Media (4-6.9) · 🟢 Baja (<4).

> ⚡ **Resumen en 30 segundos:** 5 CVEs criticos nuevos el 14/11, ninguno en KEV. Prioridad real sigue siendo Check Point Gaia + UnrealIRCd (vencimiento 16/11). Accion inmediata: parchear antes del 16/11.

---

## 1. Turno de vigilancia — qué atender primero

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 — Check Point Gaia / UnrealIRCd | Siguen en KEV, vencimiento federal **16/11**. Parchear antes de esa fecha. |
| 2 | 🟠 P2 — 5 CVEs nuevos del 14/11 | TRENDnet EOL, Shipo, Dokploy, Fluent Forms, AutomatorWP. Sin explotación: inventario y parcheo rutinario. |
| 3 | 🟡 Pendiente | Análisis de avisos ICS CISA ICSA-26-316-01/02/03 (Siemens, Node.js/PHP2Shell). |

> ℹ️ **Sin nuevas adiciones al KEV el 14/11.** La última actualización sigue siendo la del 13/11 (CVE-2026-114713 UnrealIRCd y CVE-2026-123039 Check Point Gaia).

---

## 2. Recomendaciones SOC L1

1. 🔴 **Mantener prioridad 1**: parcheo de Check Point Gaia (Jumbo Hotfix por take) y UnrealIRCd (6.2.7.1 o hot-patch) **antes del 16/11**.
2. 🟠 **Inventario rutinario** de los CVEs nuevos del 14/11 (TRENDnet EOL, plugins WP, Dokploy) y parcheo según disponibilidad del fabricante; TRENDnet EOL implica plan de reemplazo.
3. 🔍 **Seguir cazando** indicadores de la cadena Check Point (webshells TSGA/TSProxy, `destroy_v6`, movimiento SSH a ESXi, `/tmp/dynamic_socket` con permisos incorrectos).
4. 📋 **Completar análisis** de los avisos ICS pendientes (ICSA-26-316-01/02/03) en la próxima corrida.

---

## 3. Resumen ejecutivo

- **KEV sin cambios el 14/11**: no se detectó explotación confirmada nueva ese día.
- **CVEs ya informados (Check Point Gaia, UnrealIRCd, PHP2Shell, Outlook CVE-2026-124194) siguen siendo prioridad 1**: sin cambios en el estado de explotación.
- **NVD publicó ~20 CVEs nuevos el 14/11**, todos sin confirmación de explotación masiva. Los más relevantes por severidad: TRENDnet TEW-657BRM (EOL, out-of-bounds write remoto), Shipo WordPress (bypass de autenticación), Dokploy (inyección de comandos OS como root), Fluent Forms (arbitrary file write → RCE) y AutomatorWP Pro (bypass de autorización → takeover de cuenta).
- **Vigilancia ICS pendiente**: los avisos CISA ICSA-26-316-01/02/03 siguen sin análisis detallado; se mantienen en cola.

---

## 4. Novedades del 14/11 sin explotación confirmada (vigilancia)

| CVE | CVSS | Producto | Impacto | Notas |
|---|---|---|---|---|
| 🔴 CVE-2026-129351 | Crítica | TRENDnet TEW-657BRM (mini_httpd) | Out-of-bounds write remoto, sin autenticación | Producto EOL; fabricante no confirma la vulnerabilidad |
| 🔴 CVE-2026-19907 | Crítica | Plugin WordPress "Shipo" | Bypass de autenticación → impersonación de admin vía REST API | Sin explotación confirmada |
| 🔴 CVE-2026-129433 | Crítica | Dokploy | Inyección de comandos OS como root vía backup (nombre de servicio) | Requiere usuario autenticado |
| 🔴 CVE-2026-120522 | Crítica | Plugin WordPress "Fluent Forms" (cloud storage) | Arbitrary file write → RCE potencial | Endpoints REST expuestos |
| 🔴 CVE-2026-126173 | Crítica | Plugin WordPress "AutomatorWP Pro" (webhook addon) | Bypass de autorización → reset de contraseña de cualquier usuario | Requiere configuración específica de webhooks |

> ℹ️ Ninguno figura en el KEV ni tiene PoC pública confirmada a la fecha de consulta.

---

## 5. Contexto de seguimiento (sin cambios respecto al 13/11)

- **CVE-2026-123039 (Check Point Gaia)** y **CVE-2026-114713 (UnrealIRCd)**: siguen en KEV con vencimiento federal 16/11. Sin novedades de explotación adicionales reportadas el 14/11.
- **CVE-2026-124194 (Outlook, NTLMv2 leak)**: sin cambios; controles de bloqueo NTLM saliente siguen vigentes.
- **PHP2Shell (CVE-2026-109618/109619)**: sin cambios; explotación activa confirmada también contra interfaces de gestión PAN-OS.

---

## 6. Cobertura y limitaciones

- No se detectaron nuevas adiciones al KEV ni alertas CISA el 14/11; la cobertura se centró en NVD (publicaciones del día) y seguimiento de hallazgos previos.
- EPSS y estados de explotación corresponden a las fuentes citadas a la fecha de consulta (15/11/2026).

```json
{"fecha":"2026-11-14","cves":[{"id":"CVE-2026-129351","producto":"TRENDnet TEW-657BRM (mini_httpd)","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Producto EOL; fabricante no confirma la vulnerabilidad; planificar reemplazo.","runbook":"1) Identificar dispositivos TEW-657BRM expuestos. 2) Aislar de Internet. 3) Planificar reemplazo por falta de soporte. 4) Monitorear trafico anormal hacia el dispositivo."},{"id":"CVE-2026-19907","producto":"Plugin WordPress Shipo","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar o desactivar el plugin; revisar configuracion de cookies de autenticacion.","runbook":"1) Inventariar sitios WordPress con el plugin Shipo. 2) Actualizar o desactivar. 3) Revisar cuentas de administrador creadas recientemente. 4) Auditar logs REST API."},{"id":"CVE-2026-129433","producto":"Dokploy","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar Dokploy segun aviso del fabricante.","runbook":"1) Identificar instancias Dokploy. 2) Parchear. 3) Revisar operaciones de backup y nombres de servicio. 4) Auditar comandos ejecutados como root."},{"id":"CVE-2026-120522","producto":"Plugin WordPress Fluent Forms (cloud storage)","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar el plugin o desactivar la integracion de almacenamiento en la nube.","runbook":"1) Inventariar sitios con Fluent Forms. 2) Actualizar o desactivar. 3) Buscar archivos PHP subidos en directorios web. 4) Revisar endpoints REST expuestos."},{"id":"CVE-2026-126173","producto":"Plugin WordPress AutomatorWP Pro (webhook addon)","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar el plugin; revisar configuracion de webhooks.","runbook":"1) Inventariar sitios con AutomatorWP Pro. 2) Actualizar. 3) Revisar configuraciones de webhooks. 4) Auditar resets de contrasena recientes."}],"otras_amenazas":[]}
```


# ️ Informe CTI Diario — 2026-11-13

**Fecha del informe:** 14/11/2026  **Cobertura:** novedades del viernes 13 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, fabricantes oficiales (UnrealIRCd, Check Point), CERT locales (CCB Bélgica), investigación de proveedores.
**Leyenda de severidad:** 🔴 Crítica (9-10) · 🟠 Alta (7-8.9) · 🟡 Media (4-6.9) · 🟢 Baja (<4).

> ⚡ **Resumen en 30 segundos:** CISA anadio 2 CVEs al KEV el 13/11 (UnrealIRCd + Check Point Gaia), vencimiento 16/11. Cadena de ataque Check Point activa con wiper destructivo en victimas israelies. Parchear antes del 16/11.

---

## 1. Turno de vigilancia — qué atender primero

| # | Prioridad | Acción |
|---|---|---|
| 1 | 🔴 P1 — Check Point Gaia | Inventariar gateways Gaia (versión y take del Jumbo); parchear a los takes indicados o aplicar la mitigación de `dynamic_server`. Cazar: `/tmp/dynamic_socket` con permisos 0777, comandos en `dynamicCli.log` sin wrapper Clish, webshells en plantillas de login del portal, conexiones desde `130.94.19[.]204`, procesos inusuales en hosts ESXi. |
| 2 | 🔴 P1 — UnrealIRCd | Si hay servidores IRC internos/externos, verificar 6.2.7.1 o hot-patch `bundle-20261107`. |
| 3 | 🟠 P2 — Outlook | Confirmar despliegue del parche de noviembre y reglas de bloqueo de NTLM saliente; alertar sobre tráfico hacia servidores externos con auth NTLM tras apertura de adjuntos .msg. |
| 4 | 🟡 P3 — XWEB EVO / PowerStore / plugins WP | Identificar instancias expuestas; parchear según avisos del fabricante. |
| 5 | 📌 General | Las dos adiciones al KEV tienen vencimiento federal el **16/11**; usarlas como tope interno de parcheo en activos expuestos. |

> ℹ️ **CISA incorporó dos CVE con evidencia de explotación activa el 13/11.** Fecha límite de remediación federal: 16/11/2026 ([Alerta CISA](https://www.cisa.gov/news-events/alerts/2026/11/13/cisa-adds-two-known-exploited-vulnerabilities-catalog)).

---

## 2. Recomendaciones SOC L1

1. 🔴 **Prioridad 1 — Check Point**: inventariar gateways Gaia (versión y take del Jumbo); parchear a los takes indicados o aplicar la mitigación de `dynamic_server`. Cazar: `/tmp/dynamic_socket` con permisos 0777, comandos en `dynamicCli.log` sin wrapper Clish, webshells en plantillas de login del portal, conexiones desde `130.94.19[.]204`, procesos inusuales en hosts ESXi.
2. 🔴 **Prioridad 1 — UnrealIRCd**: si hay servidores IRC internos/externos, verificar 6.2.7.1 o hot-patch `bundle-20261107`.
3. 🟠 **Prioridad 2 — Outlook**: confirmar despliegue del parche de noviembre y reglas de bloqueo de NTLM saliente; alertar sobre tráfico hacia servidores externos con auth NTLM tras apertura de adjuntos .msg.
4. 🟡 **Prioridad 3 — XWEB EVO / PowerStore / plugins WordPress**: identificar instancias expuestas; parchear según avisos del fabricante.
5. 📌 **General**: las dos adiciones al KEV tienen vencimiento federal el **16/11**; usarlas como tope interno de parcheo en activos expuestos.

---

## 3. Resumen ejecutivo

- **CISA añadió dos vulnerabilidades explotadas activamente al catálogo KEV el 13/11**: CVE-2026-114713 (UnrealIRCd, inyección de comandos servidor-a-servidor) y CVE-2026-123039 (Check Point Gaia OS, escalada de privilegios a root).
- **Cadena de ataque Check Point**: PHP2Shell (CVE-2026-109618/109619) sobre portal Mobile Access → shell como cuenta del portal → root vía DynamicCLI → persistencia con webshells PHP y backdoor TSGA (con proxy TSProxy). En víctimas israelíes hubo actividad destructiva: movimiento por SSH a VMware ESXi y ejecución del wiper `destroy_v6` con notas `DREAM_ANNEXATION.txt`.
- **Novedades sin explotación confirmada el 13/11**: XWEB EVO (3 CVEs pre-autenticación), Dell PowerStore, plugins WordPress Chatterbug Forms y ELEX HelpDesk.
- **Seguimiento**: CVE-2026-124194 (Outlook, NTLMv2 leak) y PHP2Shell contra interfaces de gestión PAN-OS.

---

## 4. Hallazgos explotados (KEV)

### 4.1 CVE-2026-114713 — UnrealIRCd (inyección de comandos servidor-a-servidor)
- **CVSS:** 8.6 (Alta, CVSSv3) — **CWE-140** (neutralización incorrecta de delimitadores). El proyecto lo califica como crítico.
- **Producto afectado:** módulo `labeled-response` de UnrealIRCd **6.0.0 a 6.2.7** (corregido en **6.2.7.1**). UnrealIRCd 5 (EOL) también afectado. Requiere redes con **dos o más servidores enlazados**.
- **Mecanismo:** no se escapa la etiqueta IRCv3 `label` al ecoarla en respuestas; un usuario normal inyecta líneas extra que los servidores enlazados ejecutan **con autoridad de servidor/IRCOp** (KILL, GLINE, etc.). El equipo lo describe como el fallo más grave de los últimos 15 años en UnrealIRCd.
- **Explotación:** activa en Internet (confirmada por el proyecto; KEV el 13/11). EPSS ~0,3% (30 días).
- **Mitigación:**
  1. Aplicar la regla *spamfilter* oficial que bloquea etiquetas inválidas (protege redes 6.0.4+).
  2. Hot-patch `bundle-20261107` (sin reinicio, corrige 9 fallos) o actualizar a 6.2.7.1 y reiniciar.
  3. En v5: deshabilitar el módulo labeled-response y migrar a v6.
- **Fuentes:** [NVD CVE-2026-114713](https://nvd.nist.gov/vuln/detail/CVE-2026-114713) — [Aviso del proyecto UnrealIRCd](https://forums.unrealircd.org/viewtopic.php?t=9487) — [CCB Bélgica](https://ccb.belgium.be/advisories/warning-actively-exploited-cve-2026-114713-affects-unrealircd-patch-immediately)

### 4.2 CVE-2026-123039 — Check Point Gaia OS (escalada de privilegios a root)
- **CVSS:** 7.8 (Alta) — **CWE-732** (asignación incorrecta de permisos a recurso crítico).
- **Producto afectado:** demonio **DynamicCLI** (`dynamic_server`) de Gaia OS en Quantum Security Gateways, Management/Log Servers, Multi-Domain Security Management, Maestro Orchestrator — versiones R81, R81.10, R81.20, R82, R82.10, R82.20. **No afectados:** Spark Firewalls y Smart-1 Cloud.
- **Mecanismo:** el demonio corre como root y escucha en un socket local con permisos 0777 (`/tmp/dynamic_socket`) **sin autenticación**; cualquier usuario local autenticado ejecuta comandos arbitrarios como root escribiendo una solicitud al socket.
- **Explotación:** CISA confirmó explotación activa (KEV el 13/11). Check Point observó explotación contra "una veintena de clientes": cadena con **PHP2Shell** (CVE-2026-109618/109619, CVSSv4 9.2) sobre el portal Mobile Access → shell como cuenta del portal → root vía DynamicCLI → persistencia con webshells PHP y backdoor **TSGA** (con proxy TSProxy, variantes de PhiliKit, previamente asociado a UNC5221; Check Point no atribuye con alta confianza). En víctimas israelíes hubo actividad destructiva: movimiento por SSH a VMware ESXi y ejecución del wiper `destroy_v6` con notas `DREAM_ANNEXATION.txt`. Primera oleada 30/10; segunda oleada 02/11 sin actividad destructiva.
- **Mitigación:**
  - Jumbo Hotfix: **R82.20 Take 14+, R82.10 Take 52+, R82 Take 132+, R81.20 Take 174+**. R81.10/R81 sin take fijo: aplicar mitigación y planificar upgrade.
  - Mitigación temporal: detener `dynamic_server` e impedir su reinicio (rompe comandos Dynamic CLI de sk144112).
  - Verificar `/tmp/dynamic_socket` con permisos **0660** tras el parche; revisar `/var/log/dynamicCli.log`: todo comando que no use el wrapper Clish legítimo requiere investigación.
  - Parchear también PHP2Shell en el portal Mobile Access (cerrar solo un eslabón deja la cadena abierta).
- **Fuentes:** [SK Check Point sk1000362](https://support.checkpoint.com/results/sk/sk1000362) — [NVD CVE-2026-123039](https://nvd.nist.gov/vuln/detail/cve-2026-123039) — [Check Point Research](https://research.checkpoint.com/2026/php2shell-exploitation-on-check-point-gateway/)

---

## 5. Novedades del 13/11 sin explotación confirmada (vigilancia)

| CVE | CVSS | Producto | Impacto |
|---|---|---|---|
| 🔴 CVE-2026-124002 | 9.3 | XWEB EVO 5.2.3–5.7.0 | Inyección de comandos OS pre-autenticación |
| 🔴 CVE-2026-124010 | 9.3 | XWEB EVO < 5.6.2 | Inyección de comandos pre-autenticación |
| 🔴 CVE-2026-124006 | 9.2 | XWEB EVO ≤ 5.8.8 | Generación de credenciales predecible → credenciales root Linux derivables |
| 🔴 CVE-2026-103813 | 9.1 | Dell PowerStore | Inyección de comandos OS (usuario autenticado de bajos privilegios) |
| 🔴 CVE-2026-125055 | 9.8 | Plugin WordPress "Chatterbug Forms" | Carga arbitraria de archivos |
| 🔴 CVE-2026-119077 | 9.8 | Plugin WordPress "ELEX HelpDesk & Ticketing" | Inyección de objetos PHP |

> ℹ️ La cadena de XWEB EVO (124002/124006/124010) es especialmente peligrosa por ser pre-autenticación y permitir derivar credenciales root; los tres CVE se actualizaron el 13/11. Sin confirmación oficial de explotación masiva a la fecha.

---

## 6. Contexto reciente relevante para el SOC

- **CVE-2026-124194 (Microsoft Outlook, CVSS 6.5, "Exploitation Detected")**: zero-day de spoofing del Patch Tuesday de noviembre; abrir un archivo de mensaje manipulado envía el hash NTLMv2 al atacante sin advertencia. CISA lo añadió al KEV el 10/11. Seguimiento de hashes NTLMv2 en tráfico saliente y bloqueo de NTLM hacia Internet siguen siendo controles clave.
- **PHP2Shell (CVE-2026-109618/109619, CVSSv4 9.2)**: explotación activa confirmada también contra **interfaces de gestión PAN-OS** además de los portales Mobile Access de Check Point.

---

## 7. Cobertura y limitaciones

- Se priorizaron CISA/KEV, NVD, fabricantes y CERT; no se revisaron todos los avisos ICS publicados el 13/11 (CISA ICSA-26-316-03, 12/11, pendiente de análisis detallado).
- EPSS y contadores de explotación corresponden a las fuentes citadas a la fecha de consulta.

```json
{"fecha":"2026-11-13","cves":[{"id":"CVE-2026-114713","producto":"UnrealIRCd 6.0.0-6.2.7 (modulo labeled-response)","cvss":8.6,"severidad":"Alta","mitre":null,"explotacion":"KEV","parche":"Actualizar a 6.2.7.1 o hot-patch bundle-20261107 sin reinicio; en v5 deshabilitar labeled-response y migrar a v6.","runbook":"1) Inventariar servidores IRC y version. 2) Aplicar regla spamfilter oficial que bloquea etiquetas invalidas. 3) Parchear o actualizar y reiniciar. 4) Revisar comandos ejecutados con autoridad de IRCOp en logs."},{"id":"CVE-2026-123039","producto":"Check Point Gaia OS (DynamicCLI)","cvss":7.8,"severidad":"Alta","mitre":null,"explotacion":"KEV","parche":"Jumbo Hotfix R82.20 Take 14+, R82.10 Take 52+, R82 Take 132+, R81.20 Take 174+; verificar /tmp/dynamic_socket en 0660.","runbook":"1) Inventariar gateways Gaia y take del Jumbo. 2) Parchear o detener dynamic_server. 3) Cazar comandos en /var/log/dynamicCli.log sin wrapper Clish. 4) Buscar webshells TSGA/TSProxy y wiper destroy_v6 en ESXi."},{"id":"CVE-2026-124002","producto":"XWEB EVO 5.2.3-5.7.0","cvss":9.3,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar segun aviso del fabricante.","runbook":"1) Identificar instancias expuestas. 2) Parchear. 3) Restringir acceso a la interfaz."},{"id":"CVE-2026-124010","producto":"XWEB EVO < 5.6.2","cvss":9.3,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar segun aviso del fabricante.","runbook":"1) Identificar instancias expuestas. 2) Parchear. 3) Restringir acceso."},{"id":"CVE-2026-124006","producto":"XWEB EVO <= 5.8.8","cvss":9.2,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar segun aviso del fabricante.","runbook":"1) Identificar instancias. 2) Parchear. 3) Rotar credenciales root derivables."},{"id":"CVE-2026-103813","producto":"Dell PowerStore","cvss":9.1,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar segun aviso de Dell.","runbook":"1) Inventariar equipos. 2) Parchear. 3) Revisar cuentas de bajos privilegios."},{"id":"CVE-2026-125055","producto":"Plugin WordPress Chatterbug Forms","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar o desactivar el plugin.","runbook":"1) Inventariar sitios WordPress. 2) Actualizar o desactivar. 3) Buscar archivos subidos inesperados."},{"id":"CVE-2026-119077","producto":"Plugin WordPress ELEX HelpDesk & Ticketing","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar o desactivar el plugin.","runbook":"1) Inventariar sitios WordPress. 2) Actualizar o desactivar. 3) Buscar objetos PHP inyectados."},{"id":"CVE-2026-124194","producto":"Microsoft Outlook","cvss":6.5,"severidad":"Media","mitre":null,"explotacion":"KEV","parche":"Parche de noviembre (Patch Tuesday).","runbook":"1) Confirmar despliegue del parche. 2) Bloquear NTLM saliente. 3) Alertar sobre trafico NTLMv2 hacia externos tras abrir .msg."},{"id":"CVE-2026-109618","producto":"PHP2Shell (CVE-2026-109618/109619): portales Mobile Access de Check Point e interfaces de gestion PAN-OS","cvss":9.2,"severidad":"Critica","mitre":null,"explotacion":"explotada en la calle","parche":"Parchear portal Mobile Access e interfaces de gestion PAN-OS.","runbook":"1) Inventariar portales e interfaces de gestion expuestas. 2) Parchear. 3) Buscar webshells PHP en plantillas de login."}],"otras_amenazas":[]}
```