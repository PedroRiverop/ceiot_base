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

**MITRE Mitigation: M1041 – Encrypt Sensitive Information**  
**MITRE Mitigation: M1037 – Filter Network Traffic**

---

## 6️⃣ Command & Control (C2)

### Detección:
- **Monitoreo de conexiones salientes periódicas.**
- **Frecuencia de conexión:** detectar si un nodo se conecta más veces de lo habitual.

### Mitigación:
- **Lista blanca de destinos salientes.**
- **Timeout adaptativo.**

 **MITRE Mitigation: M1037 – Filter Network Traffic**  
 **MITRE Mitigation: M1040 – Behavior Prevention on Endpoint**

---

## 5️⃣ Installation (Persistencia)

### Detección:
- **Alertas ante creación de usuarios o cambios en roles de MongoDB.**

### Mitigación:
- **Revisión de tareas programadas (`cron`) con hash de integridad.**
- **Auditoría periódica de cuentas activas.**

 **MITRE Mitigation: M1018 – User Account Management**  
 **MITRE Mitigation: M1047 – Audit**

---

## 4️⃣ Exploitation

### Detección:
- **Verificación de integridad en scripts backend clave.**
- **Integración continua con Git para detectar cambios no autorizados.**

### Mitigación:
- **Validaciónes estricta del esquema JSON.**
- **Firmado digital de scripts backend.**

 **MITRE Mitigation: M1021 – Restrict Web-Based Content**  
 **MITRE Mitigation: M1047 – Audit**

---

## 3️⃣ Delivery

### Detección:
- **Alerta por subida de archivos `.conf` no firmados o con metadata sospechosa.**

### Mitigación:
- **Restricción por tipo y extensión en endpoints.**
- **Sandbox de archivos antes de ejecutarlos.**

 **MITRE Mitigation: M1026 – Privileged Account Management**  
 **MITRE Mitigation: M1040 – Behavior Prevention on Endpoint**

---

## 2️⃣ Weaponization

### Detección:
- **Monitoreo de rutas sensibles como `/upload/config` o `/api/export/csv`.**

### Mitigación:
- **Deshabilitar rutas no necesarias.**

 **MITRE Mitigation: M1021 – Restrict Web-Based Content**

---

## 1️⃣ Reconnaissance

### Detección:
- **Logs de acceso a rutas públicas + detección de scrapers.**

### Mitigación:
- **Ocultar detalles técnicos en documentación pública.**
- **Rate limiting.**

 **MITRE Mitigation: M1040 – Behavior Prevention on Endpoint**  
 **MITRE Mitigation: M1036 – Limit Access to Resource Over Network**

---



## Consideraciones Finales
Este plan de defensa no asume que el ataque haya comenzado, sino que prioriza la visibilidad temprana. Con controles bien aplicados, es posible detener o al menos desacelerar significativamente el progreso de un atacante avanzado.
