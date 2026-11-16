# Resumen CTI Semanal — Semana del 09 al 15 de noviembre de 2026

**Semana cubierta:** lunes 09/11/2026 al domingo 15/11/2026
**Generado:** lunes 16/11/2026 09:10 (America/Santiago)
**Fuentes:** INFORMES_DIARIOS.md (13/11, 14/11, 15/11), CISA KEV, NIST NVD, MITRE ATT&CK, fabricantes oficiales, VulnCheck KEV, Shadowserver.
**Siglas:** KEV = catalogo de vulnerabilidades explotadas conocidas de CISA · CVSS = puntuacion de severidad 0-10 · CWE = tipo de debilidad · PoC = prueba de concepto · EOL = fin de soporte · APT = actor de amenaza persistente avanzado · EPSS = probabilidad de explotacion en 30 dias.
**Leyenda de severidad:** 🔴 Critica (9-10) · 🟠 Alta (7-8.9) · 🟡 Media (4-6.9) · 🟢 Baja (<4).

> **Resumen en 30 segundos:** CISA anadio 2 CVEs al KEV el 13/11 (UnrealIRCd + Check Point Gaia), vencimiento federal 16/11. Cadena de ataque Check Point activa con wiper destructivo en victimas israelies. Windows Patch Tuesday trajo RCEs criticos (LDAP 9.8, Schannel/CredSSP/IIS/MSMQ 8.1). Dispositivos EOL (D-Link, DrayTek) con CVEs 9.9-10.0. Prioridad: parchear Check Point y UnrealIRCd antes del 16/11.

---

## 1. Turno de vigilancia — que atender primero

| # | Prioridad | Accion |
|---|---|---|
| 1 | 🔴 P1 — Check Point Gaia / UnrealIRCd | Vencimiento federal **16/11**. Verificar parche aplicado; si no, asumir compromiso y cazar IOCs. |
| 2 | 🟠 P2 — Windows Patch Tuesday (LDAP/Schannel/CredSSP/IIS/MSMQ) | CVE-2026-119958 LDAP (9.8) y otros RCEs sin KEV pero red-reachables. Parchear DCs y servidores expuestos. |
| 3 | 🟠 P3 — Dispositivos EOL (D-Link, DrayTek) | CVEs criticos 9.9-10.0 en routers EOL/sin soporte. Inventariar y reemplazar o segmentar. |
| 4 | 🟠 P4 — WordPress plugins criticos | Object injection/RCE en plugins (Auth Armor, Lazy Blocks, Document Library Pro, etc.). Inventariar sitios. |
| 5 | 🟡 General | KEV estable desde 13/11; pruebas de concepto activas en Internet. Proxima revision de catalogo el lunes. |

> ℹ️ **Sin nuevas adiciones al KEV del 14 al 15/11.** Catalogo sigue en version 2026.11.13. Ultimas adiciones: UnrealIRCd (CVE-2026-114713) y Check Point Gaia (CVE-2026-123039), vencimiento 16/11.

### Top 5 "atacar hoy"

| # | CVE | Producto | CVSS | Tiempo estimado de remediacion |
|---|---|---|---|---|
| 1 | CVE-2026-123039 | Check Point Gaia OS | 7.8 | 2-4 h (Jumbo Hotfix por take) |
| 2 | CVE-2026-114713 | UnrealIRCd 6.0.0-6.2.7 | 8.6 | 30 min (hot-patch sin reinicio) |
| 3 | CVE-2026-119958 | Windows LDAP | 9.8 | 1 h por DC (Patch Tuesday) |
| 4 | CVE-2026-129353 | D-Link DIR-600L | 10.0 | Reemplazo: 1-2 dias (EOL) |
| 5 | CVE-2026-110960 | Auth Armor <= 1.0.3 | 9.8 | 15 min (actualizar plugin) |

---

## 2. Recomendaciones SOC L1 y Recomendaciones SOC L2

### Recomendaciones SOC L1

1. 🔴 **Prioridad 1 — Check Point/UnrealIRCd:** verificar estado de parche en gateways Gaia y servidores IRC antes del 16/11; si no parcheados, cazar IOCs (webshells TSGA/TSProxy, `/tmp/dynamic_socket`, `destroy_v6`).
2. 🟠 **Prioridad 2 — Windows:** parchear controladores de dominio (LDAP CVE-2026-119958) y servidores con Schannel/CredSSP/IIS/MSMQ en este ciclo; mantener LDAP/LDAPS fuera de Internet.
3. 🟠 **Prioridad 3 — Dispositivos de borde EOL:** segmentar o reemplazar D-Link DIR-600L/DSL-6740C y DrayTek Vigor2960 con CVEs criticos sin parche confirmado.
4. 🟠 **Prioridad 4 — WordPress:** inventariar sitios con plugins criticos (Auth Armor, Lazy Blocks, Document Library Pro, Advanced Form Integration, Estatik) y actualizar o desactivar.
5. 🟡 **General:** monitorear KEV el lunes 17/11; revisar que los update rings de Microsoft 365 Apps se hayan movido (cubre CVE-2026-124194 + heap overflows).

### Recomendaciones SOC L2

1. 🟠 **Hardening:** deshabilitar MSMQ donde no se use (TCP 1801); restringir LDAP/LDAPS a segmentos confiables; revisar configuracion de Schannel en listeners TLS.
2. 🟠 **Threat hunting:** buscar actividad de wfx-loot en repositorios GitHub (campana reportada el 15/11); revisar logs de NetScaler si aplica (Citrix KEV pendientes).
3. 🟠 **Tuning de detecciones:** anadir reglas Sigma para webshells PHP (TSGA/TSProxy) y comandos DynamicCLI sin wrapper Clish; alertar sobre NTLMv2 hacia externos.
4. 🟠 **Automatizacion:** integrar EPSS en el pipeline de priorizacion; exportar catalogo CVE a SIEM para correlacion con inventario de activos.
5. 🟠 **Mejoras de proceso:** definir SLA interno de parcheo alineado a fechas KEV (BOD 26-04); documentar estado de remediacion por CVE.

---

## 3. Resumen ejecutivo

- **CISA anadio dos vulnerabilidades explotadas activamente al catalogo KEV el 13/11:** CVE-2026-114713 (UnrealIRCd, inyeccion de comandos servidor-a-servidor) y CVE-2026-123039 (Check Point Gaia OS, escalada de privilegios a root). Ambas con vencimiento federal 16/11.
- **Cadena de ataque Check Point:** PHP2Shell (CVE-2026-109618/109619) sobre portal Mobile Access → shell como cuenta del portal → root via DynamicCLI → persistencia con webshells PHP y backdoor TSGA (con proxy TSProxy). En victimas israelies hubo actividad destructiva: movimiento por SSH a VMware ESXi y ejecucion del wiper `destroy_v6` con notas `DREAM_ANNEXATION.txt`.
- **Windows Patch Tuesday (15/11):** NVD publico 112 CVEs, 15 criticos y 39 altos, ninguno en KEV. Destacan RCEs sin autenticacion en Windows (LDAP 9.8, Schannel/CredSSP/IIS/MSMQ 8.1) y dispositivos de borde EOL (D-Link, DrayTek).
- **Dispositivos EOL criticos:** D-Link DIR-600L/DSL-6740C y DrayTek Vigor2960 con CVEs 9.9-10.0 sin parche confirmado; planificar reemplazo o segmentacion.
- **WordPress:** multiples plugins con RCE/object injection criticos; inventario y parcheo rutinario.

---

## 4. Vulnerabilidades criticas de la semana

### 4.1 CVE-2026-123039 — Check Point Gaia OS (escalada de privilegios a root)

- **En simple:** Una vulnerabilidad en el demonio DynamicCLI de Check Point Gaia permite a cualquier usuario local autenticado ejecutar comandos como root sin autenticacion. CISA la anadio al KEV el 13/11. Los atacantes la usan en una cadena que empieza con PHP2Shell y termina con un wiper destructivo en victimas israelies.
- **CVE:** CVE-2026-123039 · **CVSS:** 7.8 (Alta) · **CWE:** CWE-732 · **EPSS:** ~0.3% (30 dias) · **Score combinado CVSS×EPSS:** ~2.34
- **MITRE ATT&CK:** T1068 (Exploitation for Privilege Escalation)
- **Productos afectados:** Demonio DynamicCLI (`dynamic_server`) de Gaia OS en Quantum Security Gateways, Management/Log Servers, Multi-Domain Security Management, Maestro Orchestrator — versiones R81, R81.10, R81.20, R82, R82.10, R82.20. No afectados: Spark Firewalls y Smart-1 Cloud.
- **Explotacion:** KEV (CISA, 13/11). Cadena: PHP2Shell (CVE-2026-109618/109619) sobre portal Mobile Access → shell como cuenta del portal → root via DynamicCLI → persistencia con webshells PHP y backdoor TSGA (con proxy TSProxy). En victimas israelies: movimiento por SSH a VMware ESXi y wiper `destroy_v6` con notas `DREAM_ANNEXATION.txt`.
- **IOCs:** `/tmp/dynamic_socket`, `130.94.19.204`, webshell TSGA, webshell TSProxy, `destroy_v6`, `DREAM_ANNEXATION.txt`
- **Regla Sigma/YARA:** Regla YARA: deteccion de webshells PHP TSGA/TSProxy en plantillas de login del portal Check Point.
- **Mitigacion:** Jumbo Hotfix: R82.20 Take 14+, R82.10 Take 52+, R82 Take 132+, R81.20 Take 174+. Mitigacion temporal: detener `dynamic_server`. Verificar `/tmp/dynamic_socket` con permisos 0660 tras el parche.
- **Playbook:**
  - **Triage:** Verificar version y take del Jumbo en cada gateway. Revisar `/tmp/dynamic_socket` permisos (debe ser 0660). Buscar webshells en plantillas de login.
  - **Contencion:** Aplicar Jumbo Hotfix o detener dynamic_server. Bloquear conexiones desde 130.94.19.204. Aislar gateways sospechosos.
  - **Verificacion:** Confirmar socket en 0660. Validar ausencia de comandos sin wrapper Clish en dynamicCli.log. Verificar no haya movimiento SSH a ESXi ni wiper destroy_v6.
- **Fuentes:** [SK Check Point sk1000362](https://support.checkpoint.com/results/sk/sk1000362) · [NVD CVE-2026-123039](https://nvd.nist.gov/vuln/detail/cve-2026-123039) · [Check Point Research](https://research.checkpoint.com/2026/php2shell-exploitation-on-check-point-gateway/)

### 4.2 CVE-2026-114713 — UnrealIRCd (inyeccion de comandos servidor-a-servidor)

- **En simple:** Una vulnerabilidad en el modulo labeled-response de UnrealIRCd permite a un usuario normal inyectar comandos que los servidores enlazados ejecutan con autoridad de IRCOp. El equipo lo califica como el fallo mas grave de los ultimos 15 anos en UnrealIRCd. CISA lo anadio al KEV el 13/11.
- **CVE:** CVE-2026-114713 · **CVSS:** 8.6 (Alta) · **CWE:** CWE-140 · **EPSS:** ~0.3% (30 dias) · **Score combinado CVSS×EPSS:** ~2.58
- **MITRE ATT&CK:** No documentado por MITRE para este CVE.
- **Productos afectados:** Modulo `labeled-response` de UnrealIRCd 6.0.0 a 6.2.7 (corregido en 6.2.7.1). UnrealIRCd 5 (EOL) tambien afectado. Requiere redes con dos o mas servidores enlazados.
- **Explotacion:** KEV (CISA, 13/11). Confirmada por el proyecto. Afecta redes IRC con multiples servidores enlazados.
- **Mitigacion:** (1) Aplicar regla spamfilter oficial que bloquea etiquetas invalidas. (2) Hot-patch `bundle-20261107` (sin reinicio) o actualizar a 6.2.7.1 y reiniciar. (3) En v5: deshabilitar el modulo labeled-response y migrar a v6.
- **Fuentes:** [NVD CVE-2026-114713](https://nvd.nist.gov/vuln/detail/CVE-2026-114713) · [Aviso del proyecto UnrealIRCd](https://forums.unrealircd.org/viewtopic.php?t=9487) · [CCB Belgica](https://ccb.belgium.be/advisories/warning-actively-exploited-cve-2026-114713-affects-unrealircd-patch-immediately)

### 4.3 CVE-2026-119958 — Windows LDAP (RCE sin autenticacion)

- **En simple:** Una vulnerabilidad critica en Windows LDAP permite ejecucion remota de codigo sin autenticacion sobre controladores de dominio. Es el CVE mas urgente del Patch Tuesday de noviembre por la exposicion de los DCs.
- **CVE:** CVE-2026-119958 · **CVSS:** 9.8 (Critica) · **CWE:** N/D · **EPSS:** 1.3% (30 dias) · **Score combinado CVSS×EPSS:** ~12.74
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application)
- **Productos afectados:** Windows LDAP (controladores de dominio).
- **Explotacion:** Sin explotacion confirmada en KEV. Red-reachable.
- **Mitigacion:** Parche de noviembre (Patch Tuesday); actualizar DCs. Mantener LDAP/LDAPS fuera de Internet.
- **Fuentes:** [NVD CVE-2026-119958](https://nvd.nist.gov/vuln/detail/CVE-2026-119958)

### 4.4 Dispositivos de borde EOL (D-Link, DrayTek)

- **En simple:** Multiples routers y dispositivos de borde de D-Link y DrayTek tienen CVEs criticos (9.9-10.0) sin parche disponible porque son productos EOL. Son vulnerables a stack overflow, credenciales hard-coded y command injection.
- **CVEs:** CVE-2026-129353 (D-Link DIR-600L, 10.0), CVE-2026-129354 (D-Link DIR-600L, 10.0), CVE-2026-129444 (D-Link DSL-6740C, 10.0), CVE-2026-129449/450/451/453 (DrayTek Vigor2960, 9.9)
- **MITRE ATT&CK:** T1190 (Exploit Public-Facing Application)
- **Explotacion:** Sin explotacion confirmada.
- **Mitigacion:** Segmentar o reemplazar. Sin parche disponible para productos EOL.

### 4.5 WordPress plugins criticos

- **En simple:** Mas de 30 plugins WordPress publicados el 15/11 con vulnerabilidades criticas (object injection, RCE, privilege escalation, file inclusion). Requiere inventario y parcheo rutinario.
- **CVEs destacados:** CVE-2026-122662 (Document Library Pro, 9.8), CVE-2026-125769 (Advanced Form Integration, 9.8), CVE-2026-125795 (Estatik, 9.8), CVE-2026-110960 (Auth Armor, 9.8), CVE-2026-127048 (Lazy Blocks, 9.8)
- **Explotacion:** Sin explotacion confirmada.
- **Mitigacion:** Inventariar sitios, actualizar o desactivar plugins afectados.

---

## 5. Explotacion activa y ataques en la calle

- **Cadena Check Point (CVE-2026-123039 + PHP2Shell):** explotacion activa confirmada. Primera oleada 30/10, segunda 02/11 sin actividad destructiva. Victimas israelies con wiper `destroy_v6`.
- **CVE-2026-124194 (Outlook, NTLMv2 leak):** zero-day del Patch Tuesday de noviembre; abrir un archivo .msg manipulado envia el hash NTLMv2 al atacante. CISA lo anadio al KEV el 10/11. Seguimiento de hashes NTLMv2 en trafico saliente.
- **PHP2Shell contra PAN-OS:** explotacion activa confirmada tambien contra interfaces de gestion PAN-OS.

---

## 6. Ransomware

- No se reportaron nuevas campanas de ransomware especificas esta semana en las fuentes consultadas. La actividad destructiva de la cadena Check Point (wiper `destroy_v6`) se considera ciberataque destructivo, no ransomware tradicional.

---

## 7. Otras amenazas (APT, cadenas de explotacion)

- **wfx-loot:** campana dirigida a repositorios GitHub reportada el 15/11. Buscar actividad en repositorios propios.
- **Siemens Desigo / Node2Shell (CVE-2026-111497):** advisory ICSA-26-316-02 del 12/11, pendiente de analisis detallado.
- **Citrix NetScaler (KEV pendientes):** revisar logs si aplica.

---

## 8. Tabla comparativa

| CVE | CVSS | Severidad | Explotacion | EPSS | Estado remed. | Accion |
|---|---|---|---|---|---|---|
| CVE-2026-123039 | 7.8 | 🟠 Alta | KEV | 0.3% | Abierto | Parchear Jumbo Hotfix antes 16/11 |
| CVE-2026-114713 | 8.6 | 🟠 Alta | KEV | 0.3% | Abierto | Hot-patch o actualizar antes 16/11 |
| CVE-2026-119958 | 9.8 | 🔴 Critica | Sin confirmar | 1.3% | Abierto | Parchear DCs en este ciclo |
| CVE-2026-129353 | 10.0 | 🔴 Critica | Sin confirmar | 1.8% | Abierto | Segmentar/reemplazar (EOL) |
| CVE-2026-110960 | 9.8 | 🔴 Critica | Sin confirmar | N/D | Abierto | Actualizar plugin Auth Armor |
| CVE-2026-124194 | 6.5 | 🟡 Media | KEV | N/D | Abierto | Bloquear NTLM saliente |

---

## 9. Seguimiento de remediacion

| CVE | Estado | Fecha de deteccion | Fecha de remediacion | Notas |
|---|---|---|---|---|
| CVE-2026-123039 | Abierto | 13/11/2026 | — | Vencimiento federal 16/11 |
| CVE-2026-114713 | Abierto | 13/11/2026 | — | Vencimiento federal 16/11 |
| CVE-2026-119958 | Abierto | 15/11/2026 | — | Patch Tuesday noviembre |
| CVE-2026-129353 | Abierto | 15/11/2026 | — | Producto EOL, sin parche |
| CVE-2026-110960 | Abierto | 15/11/2026 | — | Actualizar plugin |

---

## 10. Tendencia historica

- **CVEs por semana:** Semana del 09-15/11: 3 dias de reporte (13, 14, 15/11). Total de CVEs criticos reportados: ~20+ (incluyendo Windows, D-Link, DrayTek, WordPress, XWEB EVO, plugins).
- **KEV acumulados:** 2 nuevas adiciones esta semana (UnrealIRCd, Check Point Gaia). Catalogo en version 2026.11.13.
- **MTTR:** Pendiente de primer ciclo de remediacion; sin datos historicos aun.

---

## 11. Cobertura y limitaciones

- Solo se dispone de 3 diarios para esta semana (13, 14, 15/11). No hay entradas para el 09-12/11.
- EPSS y estados de explotacion corresponden a las fuentes citadas a la fecha de consulta (16/11/2026).
- No se analizaron individualmente los 39 CVEs de severidad alta de WordPress/XSS; se agruparon como rutinarios.

```json
{"fecha":"2026-11-09","cves":[{"id":"CVE-2026-123039","producto":"Check Point Gaia OS (DynamicCLI)","cvss":7.8,"severidad":"Alta","mitre":{"id":"T1068","nombre":"Exploitation for Privilege Escalation"},"explotacion":"KEV","parche":"Jumbo Hotfix R82.20 Take 14+, R82.10 Take 52+, R82 Take 132+, R81.20 Take 174+; verificar /tmp/dynamic_socket en 0660.","runbook":"1) Inventariar gateways Gaia y take del Jumbo. 2) Parchear o detener dynamic_server. 3) Cazar comandos en /var/log/dynamicCli.log sin wrapper Clish. 4) Buscar webshells TSGA/TSProxy y wiper destroy_v6 en ESXi.","epss":0.3,"iocs":["/tmp/dynamic_socket","130.94.19.204","webshell TSGA","webshell TSProxy","destroy_v6","DREAM_ANNEXATION.txt"],"sigma":"Regla YARA: deteccion de webshells PHP TSGA/TSProxy en plantillas de login del portal Check Point.","playbook":{"triage":["Verificar version y take del Jumbo en cada gateway.","Revisar /tmp/dynamic_socket permisos (debe ser 0660).","Buscar webshells en plantillas de login."],"contencion":["Aplicar Jumbo Hotfix o detener dynamic_server.","Bloquear conexiones desde 130.94.19.204.","Aislar gateways sospechosos."],"verificacion":["Confirmar socket en 0660.","Validar ausencia de comandos sin wrapper Clish en dynamicCli.log.","Verificar no haya movimiento SSH a ESXi ni wiper destroy_v6."]}},{"id":"CVE-2026-114713","producto":"UnrealIRCd 6.0.0-6.2.7 (modulo labeled-response)","cvss":8.6,"severidad":"Alta","mitre":null,"explotacion":"KEV","parche":"Actualizar a 6.2.7.1 o hot-patch bundle-20261107 sin reinicio; en v5 deshabilitar labeled-response y migrar a v6.","runbook":"1) Inventariar servidores IRC y version. 2) Aplicar regla spamfilter oficial que bloquea etiquetas invalidas. 3) Parchear o actualizar y reiniciar. 4) Revisar comandos ejecutados con autoridad de IRCOp en logs.","epss":0.3,"iocs":[],"sigma":"Regla Sigma: deteccion de etiquetas IRCv3 label invalidas en respuestas de servidores enlazados.","playbook":{"triage":["Inventariar servidores IRC y version.","Verificar si hay redes con multiples servidores enlazados."],"contencion":["Aplicar regla spamfilter oficial.","Hot-patch bundle-20261107 o actualizar a 6.2.7.1."],"verificacion":["Confirmar version 6.2.7.1 o superior.","Validar ausencia de comandos ejecutados con autoridad de IRCOp no autorizados."]}},{"id":"CVE-2026-119958","producto":"Windows LDAP","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"sin explotacion confirmada","parche":"Parche de noviembre (Patch Tuesday); actualizar DCs.","runbook":"1) Identificar controladores de dominio. 2) Aplicar parche de noviembre. 3) Mantener LDAP/LDAPS fuera de Internet. 4) Monitorear trafico LDAP anormal.","epss":0.013,"iocs":[],"sigma":"Regla Sigma: deteccion de trafico LDAP inusual hacia DCs desde segmentos no confiables.","playbook":{"triage":["Verificar version del DC y estado del parche.","Revisar logs de eventos LDAP (4662, 5136)."],"contencion":["Aislar DCs de segmentos no confiables.","Bloquear LDAP/LDAPS en firewall perimetral."],"verificacion":["Confirmar build >= version parcheada.","Validar ausencia de conexiones LDAP externas."]}},{"id":"CVE-2026-129353","producto":"D-Link DIR-600L","cvss":10.0,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"sin explotacion confirmada","parche":"Producto EOL; sin parche confirmado; planificar reemplazo o segmentacion.","runbook":"1) Inventariar dispositivos DIR-600L expuestos. 2) Segmentar o retirar de Internet. 3) Planificar reemplazo. 4) Monitorear trafico hacia el dispositivo.","epss":0.018,"iocs":[],"sigma":"Regla Sigma: deteccion de requests HTTP a /authentication.cgi o /HNAP en dispositivos D-Link.","playbook":{"triage":["Identificar dispositivos DIR-600L en inventario.","Verificar exposicion a Internet."],"contencion":["Desconectar de Internet o segmentar.","Bloquear puertos de gestion."],"verificacion":["Confirmar ausencia de trafico hacia el dispositivo desde externos.","Documentar plan de reemplazo."]}},{"id":"CVE-2026-110960","producto":"Auth Armor <= 1.0.3","cvss":9.8,"severidad":"Critica","mitre":{"id":"T1190","nombre":"Exploit Public-Facing Application"},"explotacion":"sin explotacion confirmada","parche":"Actualizar plugin segun aviso del fabricante.","runbook":"1) Inventariar sitios WordPress con Auth Armor. 2) Actualizar o desactivar. 3) Revisar cuentas creadas recientemente. 4) Auditar logs REST API.","epss":null,"iocs":[],"sigma":"Regla Sigma: deteccion de actividad anormal en plugin Auth Armor (account takeover).","playbook":{"triage":["Inventariar sitios WordPress con Auth Armor.","Verificar version del plugin."],"contencion":["Actualizar o desactivar el plugin.","Revisar cuentas de administrador creadas recientemente."],"verificacion":["Confirmar version actualizada.","Validar ausencia de actividad anomala en logs."]}},{"id":"CVE-2026-124194","producto":"Microsoft Outlook","cvss":6.5,"severidad":"Media","mitre":null,"explotacion":"KEV","parche":"Parche de noviembre (Patch Tuesday).","runbook":"1) Confirmar despliegue del parche. 2) Bloquear NTLM saliente. 3) Alertar sobre trafico NTLMv2 hacia externos tras abrir .msg.","epss":null,"iocs":[],"sigma":"Regla Sigma: deteccion de trafico NTLMv2 hacia destinos externos tras apertura de archivos .msg.","playbook":{"triage":["Verificar despliegue del parche de noviembre.","Revisar configuracion de bloqueo NTLM saliente."],"contencion":["Bloquear NTLM saliente hacia Internet.","Alertar sobre trafico NTLMv2 anormal."],"verificacion":["Confirmar parche desplegado en todos los endpoints.","Validar ausencia de hashes NTLMv2 en trafico saliente."]}}],"otras_amenazas":[{"nombre":"wfx-loot","tipo":"campana","resumen":"Campana dirigida a repositorios GitHub reportada el 15/11."},{"nombre":"PHP2Shell","tipo":"campana","resumen":"Explotacion activa contra portales Mobile Access de Check Point e interfaces de gestion PAN-OS."}]}
```

---

**Ruta del documento:** `F:\Repositorios\CyberThreatIntelligence\INFORMES_SEMANALES.md`