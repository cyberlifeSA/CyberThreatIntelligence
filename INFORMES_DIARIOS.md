# Informe CTI Diario â€” Vulnerabilidades del 13/11/2026

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
