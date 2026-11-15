# Informe CTI Diario â€” 2026-11-14

**Fecha del informe:** 15/11/2026  **Cobertura:** novedades del sÃ¡bado 14 de noviembre de 2026 (zona horaria America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, MITRE ATT&CK, CERTs y fabricantes oficiales.

---

## 1. Resumen ejecutivo

- **Sin nuevas adiciones al catÃ¡logo KEV de CISA el 14/11**: la Ãºltima actualizaciÃ³n sigue siendo la del 13/11 (CVE-2026-114713 UnrealIRCd y CVE-2026-123039 Check Point Gaia), con vencimiento federal el 16/11. No se detectÃ³ explotaciÃ³n confirmada nueva ese dÃ­a.
- **Los CVEs ya informados (Check Point Gaia, UnrealIRCd, PHP2Shell, Outlook CVE-2026-124194) siguen siendo la prioridad 1**: el seguimiento de la cadena Check Point (wiper `destroy_v6`, webshells TSGA/TSProxy, movimiento a ESXi) continÃºa vigente; no hubo cambios en el estado de explotaciÃ³n.
- **NVD publicÃ³ ~20 CVEs nuevos el 14/11**, todos sin confirmaciÃ³n de explotaciÃ³n masiva. Los mÃ¡s relevantes por severidad: TRENDnet TEW-657BRM (EOL, out-of-bounds write remoto), Shipo WordPress (bypass de autenticaciÃ³n), Dokploy (inyecciÃ³n de comandos OS como root), Fluent Forms (arbitrary file write â†’ RCE) y AutomatorWP Pro (bypass de autorizaciÃ³n â†’ takeover de cuenta).
- **Vigilancia ICS pendiente**: los avisos CISA ICSA-26-316-01/02/03 (Siemens, Node.js/PHP2Shell) siguen sin anÃ¡lisis detallado; se mantienen en cola.
- **RecomendaciÃ³n SOC L1**: mantener el foco en Check Point/UnrealIRCd (parcheo antes del 16/11) y tratar los CVEs nuevos del 14/11 como inventario/parcheo rutinario hasta que haya evidencia de explotaciÃ³n.

---

## 2. Novedades del 14/11 sin explotaciÃ³n confirmada (vigilancia)

| CVE | CVSS | Producto | Impacto | Notas |
|---|---|---|---|---|
| CVE-2026-129351 | CrÃ­tica | TRENDnet TEW-657BRM (mini_httpd) | Out-of-bounds write remoto, sin autenticaciÃ³n | Producto EOL; fabricante no confirma la vulnerabilidad |
| CVE-2026-19907 | CrÃ­tica | Plugin WordPress "Shipo" | Bypass de autenticaciÃ³n â†’ impersonaciÃ³n de admin vÃ­a REST API | Sin explotaciÃ³n confirmada |
| CVE-2026-129433 | CrÃ­tica | Dokploy | InyecciÃ³n de comandos OS como root vÃ­a backup (nombre de servicio) | Requiere usuario autenticado |
| CVE-2026-120522 | CrÃ­tica | Plugin WordPress "Fluent Forms" (cloud storage) | Arbitrary file write â†’ RCE potencial | Endpoints REST expuestos |
| CVE-2026-126173 | CrÃ­tica | Plugin WordPress "AutomatorWP Pro" (webhook addon) | Bypass de autorizaciÃ³n â†’ reset de contraseÃ±a de cualquier usuario | Requiere configuraciÃ³n especÃ­fica de webhooks |

Ninguno figura en el KEV ni tiene PoC pÃºblica confirmada a la fecha de consulta.

---

## 3. Contexto de seguimiento (sin cambios respecto al 13/11)

- **CVE-2026-123039 (Check Point Gaia)** y **CVE-2026-114713 (UnrealIRCd)**: siguen en KEV con vencimiento federal 16/11. Sin novedades de explotaciÃ³n adicionales reportadas el 14/11.
- **CVE-2026-124194 (Outlook, NTLMv2 leak)**: sin cambios; controles de bloqueo NTLM saliente siguen vigentes.
- **PHP2Shell (CVE-2026-109618/109619)**: sin cambios; explotaciÃ³n activa confirmada tambiÃ©n contra interfaces de gestiÃ³n PAN-OS.

---

## 4. Recomendaciones SOC L1

1. **Mantener prioridad 1**: parcheo de Check Point Gaia (Jumbo Hotfix por take) y UnrealIRCd (6.2.7.1 o hot-patch) antes del 16/11.
2. **Inventario rutinario** de los CVEs nuevos del 14/11 (TRENDnet EOL, plugins WP, Dokploy) y parcheo segÃºn disponibilidad del fabricante; TRENDnet EOL implica plan de reemplazo.
3. **Seguir cazando** indicadores de la cadena Check Point (webshells TSGA/TSProxy, `destroy_v6`, movimiento SSH a ESXi, `/tmp/dynamic_socket` con permisos incorrectos).
4. **Completar anÃ¡lisis** de los avisos ICS pendientes (ICSA-26-316-01/02/03) en la prÃ³xima corrida.

---

## 5. Cobertura y limitaciones

- No se detectaron nuevas adiciones al KEV ni alertas CISA el 14/11; la cobertura se centrÃ³ en NVD (publicaciones del dÃ­a) y seguimiento de hallazgos previos.
- EPSS y estados de explotaciÃ³n corresponden a las fuentes citadas a la fecha de consulta (15/11/2026).

```json
{"fecha":"2026-11-14","cves":[{"id":"CVE-2026-129351","producto":"TRENDnet TEW-657BRM (mini_httpd)","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Producto EOL; fabricante no confirma la vulnerabilidad; planificar reemplazo.","runbook":"1) Identificar dispositivos TEW-657BRM expuestos. 2) Aislar de Internet. 3) Planificar reemplazo por falta de soporte. 4) Monitorear trafico anormal hacia el dispositivo."},{"id":"CVE-2026-19907","producto":"Plugin WordPress Shipo","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar o desactivar el plugin; revisar configuracion de cookies de autenticacion.","runbook":"1) Inventariar sitios WordPress con el plugin Shipo. 2) Actualizar o desactivar. 3) Revisar cuentas de administrador creadas recientemente. 4) Auditar logs REST API."},{"id":"CVE-2026-129433","producto":"Dokploy","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar Dokploy segun aviso del fabricante.","runbook":"1) Identificar instancias Dokploy. 2) Parchear. 3) Revisar operaciones de backup y nombres de servicio. 4) Auditar comandos ejecutados como root."},{"id":"CVE-2026-120522","producto":"Plugin WordPress Fluent Forms (cloud storage)","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar el plugin o desactivar la integracion de almacenamiento en la nube.","runbook":"1) Inventariar sitios con Fluent Forms. 2) Actualizar o desactivar. 3) Buscar archivos PHP subidos en directorios web. 4) Revisar endpoints REST expuestos."},{"id":"CVE-2026-126173","producto":"Plugin WordPress AutomatorWP Pro (webhook addon)","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar el plugin; revisar configuracion de webhooks.","runbook":"1) Inventariar sitios con AutomatorWP Pro. 2) Actualizar. 3) Revisar configuraciones de webhooks. 4) Auditar resets de contrasena recientes."}],"otras_amenazas":[]}
```

# Informe CTI Diario â€” 2026-11-13

**Fecha del informe:** 14/11/2026 Â· **Cobertura:** novedades del viernes 13 de noviembre de 2026 (zona horaria America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, fabricantes oficiales (UnrealIRCd, Check Point), CERT locales (CCB BÃ©lgica), investigaciÃ³n de proveedores.

---

## 1. CISA aÃ±ade dos vulnerabilidades explotadas activamente al catÃ¡logo KEV (13/11/2026)

CISA incorporÃ³ dos CVE con evidencia de explotaciÃ³n activa. **Fecha lÃ­mite de remediaciÃ³n federal: 16/11/2026** ([Alerta CISA](https://www.cisa.gov/news-events/alerts/2026/11/13/cisa-adds-two-known-exploited-vulnerabilities-catalog)).

### 1.1 CVE-2026-114713 â€” UnrealIRCd (inyecciÃ³n de comandos servidor-a-servidor)
- **CVSS:** 8.6 (Alta, CVSSv3) Â· **CWE-140** (neutralizaciÃ³n incorrecta de delimitadores). El proyecto lo califica como crÃ­tico.
- **Producto afectado:** mÃ³dulo `labeled-response` de UnrealIRCd **6.0.0 a 6.2.7** (corregido en **6.2.7.1**). UnrealIRCd 5 (EOL) tambiÃ©n afectado. Requiere redes con **dos o mÃ¡s servidores enlazados**.
- **Mecanismo:** no se escapa la etiqueta IRCv3 `label` al ecoarla en respuestas; un usuario normal inyecta lÃ­neas extra que los servidores enlazados ejecutan **con autoridad de servidor/IRCOp** (KILL, GLINE, etc.). El equipo lo describe como el fallo mÃ¡s grave de los Ãºltimos 15 aÃ±os en UnrealIRCd.
- **ExplotaciÃ³n:** activa en Internet (confirmada por el proyecto; KEV el 13/11). EPSS ~0,3% (30 dÃ­as).
- **MitigaciÃ³n:**
  1. Aplicar la regla *spamfilter* oficial que bloquea etiquetas invÃ¡lidas (protege redes 6.0.4+).
  2. Hot-patch `bundle-20261107` (sin reinicio, corrige 9 fallos) o actualizar a 6.2.7.1 y reiniciar.
  3. En v5: deshabilitar el mÃ³dulo labeled-response y migrar a v6.
- **Fuentes:** [NVD CVE-2026-114713](https://nvd.nist.gov/vuln/detail/CVE-2026-114713) Â· [Aviso del proyecto UnrealIRCd](https://forums.unrealircd.org/viewtopic.php?t=9487) Â· [CCB BÃ©lgica](https://ccb.belgium.be/advisories/warning-actively-exploited-cve-2026-114713-affects-unrealircd-patch-immediately)

### 1.2 CVE-2026-123039 â€” Check Point Gaia OS (escalada de privilegios a root)
- **CVSS:** 7.8 (Alta) Â· **CWE-732** (asignaciÃ³n incorrecta de permisos a recurso crÃ­tico).
- **Producto afectado:** demonio **DynamicCLI** (`dynamic_server`) de Gaia OS en Quantum Security Gateways, Management/Log Servers, Multi-Domain Security Management, Maestro Orchestrator â€” versiones R81, R81.10, R81.20, R82, R82.10, R82.20. **No afectados:** Spark Firewalls y Smart-1 Cloud.
- **Mecanismo:** el demonio corre como root y escucha en un socket local con permisos 0777 (`/tmp/dynamic_socket`) **sin autenticaciÃ³n**; cualquier usuario local autenticado ejecuta comandos arbitrarios como root escribiendo una solicitud al socket.
- **ExplotaciÃ³n:** CISA confirmÃ³ explotaciÃ³n activa (KEV el 13/11). Check Point observÃ³ explotaciÃ³n contra "una veintena de clientes": cadena con **PHP2Shell** (CVE-2026-109618/109619, CVSSv4 9.2) sobre el portal Mobile Access â†’ shell como cuenta del portal â†’ root vÃ­a DynamicCLI â†’ persistencia con webshells PHP y backdoor **TSGA** (con proxy TSProxy, variantes de PhiliKit, previamente asociado a UNC5221; Check Point no atribuye con alta confianza). En vÃ­ctimas israelÃ­es hubo actividad destructiva: movimiento por SSH a VMware ESXi y ejecuciÃ³n del wiper `destroy_v6` con notas `DREAM_ANNEXATION.txt`. Primera oleada 30/10; segunda oleada 02/11 sin actividad destructiva.
- **MitigaciÃ³n:**
  - Jumbo Hotfix: **R82.20 Take 14+, R82.10 Take 52+, R82 Take 132+, R81.20 Take 174+**. R81.10/R81 sin take fijo: aplicar mitigaciÃ³n y planificar upgrade.
  - MitigaciÃ³n temporal: detener `dynamic_server` e impedir su reinicio (rompe comandos Dynamic CLI de sk144112).
  - Verificar `/tmp/dynamic_socket` con permisos **0660** tras el parche; revisar `/var/log/dynamicCli.log`: todo comando que no use el wrapper Clish legÃ­timo requiere investigaciÃ³n.
  - Parchear tambiÃ©n PHP2Shell en el portal Mobile Access (cerrar solo un eslabÃ³n deja la cadena abierta).
- **Fuentes:** [SK Check Point sk1000362](https://support.checkpoint.com/results/sk/sk1000362) Â· [NVD CVE-2026-123039](https://nvd.nist.gov/vuln/detail/cve-2026-123039) Â· [Check Point Research](https://research.checkpoint.com/2026/php2shell-exploitation-on-check-point-gateway/)

---

## 2. Novedades del 13/11 sin explotaciÃ³n confirmada (vigilancia)

| CVE | CVSS | Producto | Impacto |
|---|---|---|---|
| CVE-2026-124002 | 9.3 | XWEB EVO 5.2.3â€“5.7.0 | InyecciÃ³n de comandos OS **pre-autenticaciÃ³n** |
| CVE-2026-124010 | 9.3 | XWEB EVO < 5.6.2 | InyecciÃ³n de comandos pre-autenticaciÃ³n |
| CVE-2026-124006 | 9.2 | XWEB EVO â‰¤ 5.8.8 | GeneraciÃ³n de credenciales predecible â†’ credenciales root Linux derivables |
| CVE-2026-103813 | 9.1 | Dell PowerStore | InyecciÃ³n de comandos OS (usuario autenticado de bajos privilegios) |
| CVE-2026-125055 | 9.8 | Plugin WordPress "Chatterbug Forms" | Carga arbitraria de archivos |
| CVE-2026-119077 | 9.8 | Plugin WordPress "ELEX HelpDesk & Ticketing" | InyecciÃ³n de objetos PHP |

Nota: la cadena de XWEB EVO (124002/124006/124010) es especialmente peligrosa por ser pre-autenticaciÃ³n y permitir derivar credenciales root; los tres CVE se actualizaron el 13/11. Sin confirmaciÃ³n oficial de explotaciÃ³n masiva a la fecha.

---

## 3. Contexto reciente relevante para el SOC (novedades desde hallazgos previos)

- **CVE-2026-124194 (Microsoft Outlook, CVSS 6.5, "Exploitation Detected"):** zero-day de spoofing del Patch Tuesday de noviembre; abrir un archivo de mensaje manipulado envÃ­a el hash **NTLMv2** al atacante sin advertencia. CISA lo aÃ±adiÃ³ al KEV el 10/11. Seguimiento de hashes NTLMv2 en trÃ¡fico saliente y bloqueo de NTLM hacia Internet siguen siendo controles clave.
- **PHP2Shell (CVE-2026-109618/109619, CVSSv4 9.2):** explotaciÃ³n activa confirmada tambiÃ©n contra **interfaces de gestiÃ³n PAN-OS** ademÃ¡s de los portales Mobile Access de Check Point.

---

## 4. Recomendaciones SOC L1 (turno de vigilancia)

1. **Prioridad 1 â€” Check Point:** inventariar gateways Gaia (versiÃ³n y take del Jumbo); parchear a los takes indicados o aplicar la mitigaciÃ³n de `dynamic_server`. Cazar: `/tmp/dynamic_socket` â‰  0660, comandos en `dynamicCli.log` sin wrapper Clish, webshells en plantillas de login del portal, conexiones desde `130.94.19[.]204`, procesos inusuales en hosts ESXi.
2. **Prioridad 1 â€” UnrealIRCd:** si hay servidores IRC internos/externos, verificar 6.2.7.1 o hot-patch `bundle-20261107`.
3. **Prioridad 2 â€” Outlook:** confirmar despliegue del parche de noviembre y reglas de bloqueo de NTLM saliente; alertar sobre traffic hacia servidores externos con auth NTLM tras apertura de adjuntos .msg.
4. **Prioridad 3 â€” XWEB EVO / PowerStore / plugins WordPress:** identificar instancias expuestas; parchear segÃºn avisos del fabricante.
5. **General:** las dos adiciones al KEV tienen vencimiento federal el **16/11**; usarlas como tope interno de parcheo en activos expuestos.

---

## 5. Cobertura y limitaciones

- Se priorizaron CISA/KEV, NVD, fabricantes y CERT; no se revisaron todos los avisos ICS publicados el 13/11 (CISA ICSA-26-316-03, 12/11, pendiente de anÃ¡lisis detallado).
- EPSS y contadores de explotaciÃ³n corresponden a las fuentes citadas a la fecha de consulta.

```json
{"fecha":"2026-11-13","cves":[{"id":"CVE-2026-114713","producto":"UnrealIRCd 6.0.0-6.2.7 (modulo labeled-response)","cvss":8.6,"severidad":"Alta","mitre":null,"explotacion":"KEV","parche":"Actualizar a 6.2.7.1 o hot-patch bundle-20261107 sin reinicio; en v5 deshabilitar labeled-response y migrar a v6.","runbook":"1) Inventariar servidores IRC y version. 2) Aplicar regla spamfilter oficial que bloquea etiquetas invalidas. 3) Parchear o actualizar y reiniciar. 4) Revisar comandos ejecutados con autoridad de IRCOp en logs."},{"id":"CVE-2026-123039","producto":"Check Point Gaia OS (DynamicCLI)","cvss":7.8,"severidad":"Alta","mitre":null,"explotacion":"KEV","parche":"Jumbo Hotfix R82.20 Take 14+, R82.10 Take 52+, R82 Take 132+, R81.20 Take 174+; verificar /tmp/dynamic_socket en 0660.","runbook":"1) Inventariar gateways Gaia y take del Jumbo. 2) Parchear o detener dynamic_server. 3) Cazar comandos en /var/log/dynamicCli.log sin wrapper Clish. 4) Buscar webshells TSGA/TSProxy y wiper destroy_v6 en ESXi."},{"id":"CVE-2026-124002","producto":"XWEB EVO 5.2.3-5.7.0","cvss":9.3,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar segun aviso del fabricante.","runbook":"1) Identificar instancias expuestas. 2) Parchear. 3) Restringir acceso a la interfaz."},{"id":"CVE-2026-124010","producto":"XWEB EVO < 5.6.2","cvss":9.3,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar segun aviso del fabricante.","runbook":"1) Identificar instancias expuestas. 2) Parchear. 3) Restringir acceso."},{"id":"CVE-2026-124006","producto":"XWEB EVO <= 5.8.8","cvss":9.2,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar segun aviso del fabricante.","runbook":"1) Identificar instancias. 2) Parchear. 3) Rotar credenciales root derivables."},{"id":"CVE-2026-103813","producto":"Dell PowerStore","cvss":9.1,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar segun aviso de Dell.","runbook":"1) Inventariar equipos. 2) Parchear. 3) Revisar cuentas de bajos privilegios."},{"id":"CVE-2026-125055","producto":"Plugin WordPress Chatterbug Forms","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar o desactivar el plugin.","runbook":"1) Inventariar sitios WordPress. 2) Actualizar o desactivar. 3) Buscar archivos subidos inesperados."},{"id":"CVE-2026-119077","producto":"Plugin WordPress ELEX HelpDesk & Ticketing","cvss":9.8,"severidad":"Critica","mitre":null,"explotacion":"sin explotacion confirmada","parche":"Actualizar o desactivar el plugin.","runbook":"1) Inventariar sitios WordPress. 2) Actualizar o desactivar. 3) Buscar objetos PHP inyectados."},{"id":"CVE-2026-124194","producto":"Microsoft Outlook","cvss":6.5,"severidad":"Media","mitre":null,"explotacion":"KEV","parche":"Parche de noviembre (Patch Tuesday).","runbook":"1) Confirmar despliegue del parche. 2) Bloquear NTLM saliente. 3) Alertar sobre trafico NTLMv2 hacia externos tras abrir .msg."},{"id":"CVE-2026-109618","producto":"PHP2Shell (CVE-2026-109618/109619): portales Mobile Access de Check Point e interfaces de gestion PAN-OS","cvss":9.2,"severidad":"Critica","mitre":null,"explotacion":"explotada en la calle","parche":"Parchear portal Mobile Access e interfaces de gestion PAN-OS.","runbook":"1) Inventariar portales e interfaces de gestion expuestas. 2) Parchear. 3) Buscar webshells PHP en plantillas de login."}],"otras_amenazas":[]}
```
