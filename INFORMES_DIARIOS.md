# 📋 Informe CTI Diario — 2026-11-15

**Fecha del informe:** 16/11/2026  **Cobertura:** novedades del sábado 15 de noviembre de 2026 (America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs, fabricantes oficiales, VulnCheck KEV, Shadowserver, GitHub Security Advisories.
**Siglas:** KEV = catálogo de vulnerabilidades explotadas conocidas de CISA · CVSS = puntuación de severidad 0-10 · CWE = tipo de debilidad · PoC = prueba de concepto · EOL = fin de soporte · APT = actor de amenaza persistente avanzado · EPSS = probabilidad de explotación en 30 días.
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
**Siglas:** KEV = catálogo de vulnerabilidades explotadas conocidas de CISA · CVSS = puntuación de severidad 0-10 · CWE = tipo de debilidad · PoC = prueba de concepto · EOL = fin de soporte · APT = actor de amenaza persistente avanzado.
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
**Siglas:** KEV = catálogo de vulnerabilidades explotadas conocidas de CISA · CVSS = puntuación de severidad 0-10 · CWE = tipo de debilidad · PoC = prueba de concepto · EOL = fin de soporte · APT = actor de amenaza persistente avanzado.
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