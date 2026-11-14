# Informe CTI Diario — Vulnerabilidades del 13/11/2026

**Fecha del informe:** 14/11/2026 · **Cobertura:** novedades del viernes 13 de noviembre de 2026 (zona horaria America/Santiago)
**Fuentes:** CISA (KEV/Alertas), NIST NVD, fabricantes oficiales (UnrealIRCd, Check Point), CERT locales (CCB Bélgica), investigación de proveedores.

---

## 1. CISA añade dos vulnerabilidades explotadas activamente al catálogo KEV (13/11/2026)

CISA incorporó dos CVE con evidencia de explotación activa. **Fecha límite de remediación federal: 16/11/2026** ([Alerta CISA](https://www.cisa.gov/news-events/alerts/2026/11/13/cisa-adds-two-known-exploited-vulnerabilities-catalog)).

### 1.1 CVE-2026-114713 — UnrealIRCd (inyección de comandos servidor-a-servidor)
- **CVSS:** 8.6 (Alta, CVSSv3) · **CWE-140** (neutralización incorrecta de delimitadores). El proyecto lo califica como crítico.
- **Producto afectado:** módulo `labeled-response` de UnrealIRCd **6.0.0 a 6.2.7** (corregido en **6.2.7.1**). UnrealIRCd 5 (EOL) también afectado. Requiere redes con **dos o más servidores enlazados**.
- **Mecanismo:** no se escapa la etiqueta IRCv3 `label` al ecoarla en respuestas; un usuario normal inyecta líneas extra que los servidores enlazados ejecutan **con autoridad de servidor/IRCOp** (KILL, GLINE, etc.). El equipo lo describe como el fallo más grave de los últimos 15 años en UnrealIRCd.
- **Explotación:** activa en Internet (confirmada por el proyecto; KEV el 13/11). EPSS ~0,3% (30 días).
- **Mitigación:**
  1. Aplicar la regla *spamfilter* oficial que bloquea etiquetas inválidas (protege redes 6.0.4+).
  2. Hot-patch `bundle-20261107` (sin reinicio, corrige 9 fallos) o actualizar a 6.2.7.1 y reiniciar.
  3. En v5: deshabilitar el módulo labeled-response y migrar a v6.
- **Fuentes:** [NVD CVE-2026-114713](https://nvd.nist.gov/vuln/detail/CVE-2026-114713) · [Aviso del proyecto UnrealIRCd](https://forums.unrealircd.org/viewtopic.php?t=9487) · [CCB Bélgica](https://ccb.belgium.be/advisories/warning-actively-exploited-cve-2026-114713-affects-unrealircd-patch-immediately)

### 1.2 CVE-2026-123039 — Check Point Gaia OS (escalada de privilegios a root)
- **CVSS:** 7.8 (Alta) · **CWE-732** (asignación incorrecta de permisos a recurso crítico).
- **Producto afectado:** demonio **DynamicCLI** (`dynamic_server`) de Gaia OS en Quantum Security Gateways, Management/Log Servers, Multi-Domain Security Management, Maestro Orchestrator — versiones R81, R81.10, R81.20, R82, R82.10, R82.20. **No afectados:** Spark Firewalls y Smart-1 Cloud.
- **Mecanismo:** el demonio corre como root y escucha en un socket local con permisos 0777 (`/tmp/dynamic_socket`) **sin autenticación**; cualquier usuario local autenticado ejecuta comandos arbitrarios como root escribiendo una solicitud al socket.
- **Explotación:** CISA confirmó explotación activa (KEV el 13/11). Check Point observó explotación contra "una veintena de clientes": cadena con **PHP2Shell** (CVE-2026-109618/109619, CVSSv4 9.2) sobre el portal Mobile Access → shell como cuenta del portal → root vía DynamicCLI → persistencia con webshells PHP y backdoor **TSGA** (con proxy TSProxy, variantes de PhiliKit, previamente asociado a UNC5221; Check Point no atribuye con alta confianza). En víctimas israelíes hubo actividad destructiva: movimiento por SSH a VMware ESXi y ejecución del wiper `destroy_v6` con notas `DREAM_ANNEXATION.txt`. Primera oleada 30/10; segunda oleada 02/11 sin actividad destructiva.
- **Mitigación:**
  - Jumbo Hotfix: **R82.20 Take 14+, R82.10 Take 52+, R82 Take 132+, R81.20 Take 174+**. R81.10/R81 sin take fijo: aplicar mitigación y planificar upgrade.
  - Mitigación temporal: detener `dynamic_server` e impedir su reinicio (rompe comandos Dynamic CLI de sk144112).
  - Verificar `/tmp/dynamic_socket` con permisos **0660** tras el parche; revisar `/var/log/dynamicCli.log`: todo comando que no use el wrapper Clish legítimo requiere investigación.
  - Parchear también PHP2Shell en el portal Mobile Access (cerrar solo un eslabón deja la cadena abierta).
- **Fuentes:** [SK Check Point sk1000362](https://support.checkpoint.com/results/sk/sk1000362) · [NVD CVE-2026-123039](https://nvd.nist.gov/vuln/detail/cve-2026-123039) · [Check Point Research](https://research.checkpoint.com/2026/php2shell-exploitation-on-check-point-gateway/)

---

## 2. Novedades del 13/11 sin explotación confirmada (vigilancia)

| CVE | CVSS | Producto | Impacto |
|---|---|---|---|
| CVE-2026-124002 | 9.3 | XWEB EVO 5.2.3–5.7.0 | Inyección de comandos OS **pre-autenticación** |
| CVE-2026-124010 | 9.3 | XWEB EVO < 5.6.2 | Inyección de comandos pre-autenticación |
| CVE-2026-124006 | 9.2 | XWEB EVO ≤ 5.8.8 | Generación de credenciales predecible → credenciales root Linux derivables |
| CVE-2026-103813 | 9.1 | Dell PowerStore | Inyección de comandos OS (usuario autenticado de bajos privilegios) |
| CVE-2026-125055 | 9.8 | Plugin WordPress "Chatterbug Forms" | Carga arbitraria de archivos |
| CVE-2026-119077 | 9.8 | Plugin WordPress "ELEX HelpDesk & Ticketing" | Inyección de objetos PHP |

Nota: la cadena de XWEB EVO (124002/124006/124010) es especialmente peligrosa por ser pre-autenticación y permitir derivar credenciales root; los tres CVE se actualizaron el 13/11. Sin confirmación oficial de explotación masiva a la fecha.

---

## 3. Contexto reciente relevante para el SOC (novedades desde hallazgos previos)

- **CVE-2026-124194 (Microsoft Outlook, CVSS 6.5, "Exploitation Detected"):** zero-day de spoofing del Patch Tuesday de noviembre; abrir un archivo de mensaje manipulado envía el hash **NTLMv2** al atacante sin advertencia. CISA lo añadió al KEV el 10/11. Seguimiento de hashes NTLMv2 en tráfico saliente y bloqueo de NTLM hacia Internet siguen siendo controles clave.
- **PHP2Shell (CVE-2026-109618/109619, CVSSv4 9.2):** explotación activa confirmada también contra **interfaces de gestión PAN-OS** además de los portales Mobile Access de Check Point.

---

## 4. Recomendaciones SOC L1 (turno de vigilancia)

1. **Prioridad 1 — Check Point:** inventariar gateways Gaia (versión y take del Jumbo); parchear a los takes indicados o aplicar la mitigación de `dynamic_server`. Cazar: `/tmp/dynamic_socket` ≠ 0660, comandos en `dynamicCli.log` sin wrapper Clish, webshells en plantillas de login del portal, conexiones desde `130.94.19[.]204`, procesos inusuales en hosts ESXi.
2. **Prioridad 1 — UnrealIRCd:** si hay servidores IRC internos/externos, verificar 6.2.7.1 o hot-patch `bundle-20261107`.
3. **Prioridad 2 — Outlook:** confirmar despliegue del parche de noviembre y reglas de bloqueo de NTLM saliente; alertar sobre traffic hacia servidores externos con auth NTLM tras apertura de adjuntos .msg.
4. **Prioridad 3 — XWEB EVO / PowerStore / plugins WordPress:** identificar instancias expuestas; parchear según avisos del fabricante.
5. **General:** las dos adiciones al KEV tienen vencimiento federal el **16/11**; usarlas como tope interno de parcheo en activos expuestos.

---

## 5. Cobertura y limitaciones

- Se priorizaron CISA/KEV, NVD, fabricantes y CERT; no se revisaron todos los avisos ICS publicados el 13/11 (CISA ICSA-26-316-03, 12/11, pendiente de análisis detallado).
- EPSS y contadores de explotación corresponden a las fuentes citadas a la fecha de consulta.
