# Cyber Kill Chain – Defensa

## Autor
**Nombre:** Ing. José Pedro Rivero Peña  
**Especialización:** Internet de las Cosas (IoT)  
**Universidad:** Universidad de Buenos Aires (UBA)  
**Año:** 2025  

## Descripción
Este documento propone un esquema defensivo en respuesta al ataque de espionaje acústico desarrollado previamente. La defensa se enfoca en detectar y mitigar las acciones del atacante, considerando recursos limitados, sin asumir que el ataque ya ocurrió.

Las contramedidas están organizadas en orden inverso al ataque. 

## Objetivo
Diseñar una estrategia de defensa efectiva, basada en medidas clave de detección y mitigación para cada etapa del Cyber Kill Chain. El foco se centra en impedir o frenar el espionaje de datos acústicos en el objetivo sistema IoT.



## 7️⃣ Actions on Objectives

### Detección:
- **Análisis de tráfico saliente:** alertas sobre conexiones HTTPS inusuales desde el backend hacia IPs no autorizadas.
- **Política de volumen de datos:** alertar si un endpoint genera mucho tráfico sobretodo si suele enviar poco (como un nodo).

### Mitigación:
- **Cifrado de datos en reposo con claves rotativas.**
- **Límites por volumen de datos por endpoint.**

**MITRE Mitigation: M1041 – Encrypt Sensitive Information**  https://attack.mitre.org/mitigations/M1041/
**MITRE Mitigation: M1037 – Filter Network Traffic** https://attack.mitre.org/mitigations/M1037/

---

## 6️⃣ Command & Control (C2)

### Detección:
- **Monitoreo de conexiones salientes periódicas.** Revisamos si hay conexiones salientes en horarios raros
- **Frecuencia de conexión:** detectar si un nodo se conecta más veces de lo habitual.

### Mitigación:
- **Lista blanca de destinos salientes.** Solo IPs o dominios de confianza
- **Timeout adaptativo.**

 **MITRE Mitigation: M1037 – Filter Network Traffic**  https://attack.mitre.org/mitigations/M1037/
 Vuelve a ser adecuada. Controlar destinos e IP evita que el malware se comunique con su servidor de C2.
 
 **MITRE Mitigation: M1040 – Behavior Prevention on Endpoint** https://attack.mitre.org/mitigations/M1040/

---

## 5️⃣ Installation

### Detección:
- **Alertas ante creación de usuarios o cambios en roles de MongoDB.** 

### Mitigación:
- **Revisión de tareas programadas (`cron`) con hash de integridad.** Comparamos los archivos críticos con hashes de integridad para ver si cambiaron.
- **Auditoría periódica de cuentas activas.**

 **MITRE Mitigation: M1018 – User Account Management**  https://attack.mitre.org/mitigations/M1018/
 **MITRE Mitigation: M1047 – Audit**  https://attack.mitre.org/mitigations/M1047/

---

## 4️⃣ Exploitation

### Detección:
- **Verificación de integridad en scripts backend clave.** Verificar si los archivos del backend fueron modificados
- **Integración continua con Git para detectar cambios no autorizados.**

### Mitigación:
- **Validaciónes estricta del esquema JSON.**
- **Firmado digital de scripts backend.** Si se intenta enviar un JSON con campos adicionales (como extra), se rechaza.

 **MITRE Mitigation: M1021 – Restrict Web-Based Content**  https://attack.mitre.org/mitigations/M1021/
 **MITRE Mitigation: M1047 – Audit** https://attack.mitre.org/mitigations/M1047/
  Detectar cambios en scripts clave a través de auditoría.
  
---

## 3️⃣ Delivery

### Detección:
- **Alerta por subida de archivos `.conf` no firmados o con metadata sospechosa.**

### Mitigación:
- **Restricción por tipo y extensión en endpoints.**
- **Sandbox de archivos antes de ejecutarlos.** Implementamos un sandbox que prueba los archivos antes de aplicarlos.

 

---

## 2️⃣ Weaponization

### Detección:
- **Monitoreo de rutas sensibles como `/upload/config` o `/api/export/csv`.**

### Mitigación:
- **Deshabilitar rutas no necesarias.**

 **MITRE Mitigation: M1021 – Restrict Web-Based Content** https://attack.mitre.org/mitigations/M1021/

---

## 1️⃣ Reconnaissance

### Detección:
- **Logs de acceso a rutas públicas + detección de scrapers.** Ver si hay herramientas automáticas accediendo (como curl, whatweb, bots)

### Mitigación:
- **Ocultar detalles técnicos en documentación pública.**
- **Rate limiting.**
 
 **MITRE Mitigation: M1036 – Limit Access to Resource Over Network** https://attack.mitre.org/mitigations/M1035/

---



## Consideraciones Finales
Este plan de defensa no asume que el ataque haya comenzado, sino que prioriza la visibilidad temprana. Con controles bien aplicados, es posible detener o al menos desacelerar significativamente el progreso de un atacante avanzado.
