# Ejercicio Cyber Kill Chain – Ataque

## Autor
**Nombre:** Ing. José Pedro Rivero Peña  
**Especialización:** Internet de las Cosas (IoT)  
**Universidad:** Universidad de Buenos Aires (UBA)  
**Año:** 2025  

## 📡 Sistema Víctima
Sistema IoT de monitoreo de niveles de ruido en entornos cerrados o pequeñas áreas urbanas. Utiliza sensores conectados a microcontroladores ESP32 con conectividad Wi-Fi y LoRa, enviando los datos a una base de datos en la nube. El acceso a la información se realiza a través de un dashboard web, desde donde se configuran umbrales de alerta y se visualizan datos históricos.

## Objetivo del Ataque
El objetivo de este ataque es doble: por un lado, sabotear la confiabilidad del sistema de monitoreo acústico para generar desconfianza en clientes institucionales, comprometiendo la integridad de sus mediciones y su reputación; por el otro, capturar y exfiltrar datos acústicos sensibles que podrían ser utilizados en campañas de extorsión o espionaje industrial. La premisa parte del supuesto de que los datos de ruido pueden ser correlacionados con actividad humana o patrones que revelen dinámicas internas de organizaciones. Además, se busca mantener persistencia dentro del sistema comprometido para preparar futuros ataques.

## Resolución del Ataque: Cyber Kill Chain

## Resolución del Ataque: Cyber Kill Chain

### 1️⃣ Reconnaissance (Reconocimiento)
Se explora documentación pública, redes sociales y GitHub en busca de información del equipo técnicopara identificar las tecnologías utilizadas (ESP32, JWT), correos electrónicos y endpoints accesibles. Esto va a permitir perfilar objetivos concretos para spear phishing y entender la arquitectura del sistema.

- **Técnicas ATT&CK utilizadas:**
  - T1592.002 – Gather Victim Identity Information: Email Addresses  
    https://attack.mitre.org/techniques/T1592/002/
  - T1593 – Search Open Websites/Domains  
    https://attack.mitre.org/techniques/T1593/
    
### 2️⃣ Weaponization (Armado del Ataque)

Clono la interfaz del dashboard y preparo correos electrónicos con enlaces maliciosos personalizados con el objetivo de descubrir si algunas variables sensibles del sistema se pasaan en la URL, obtener pistas sobre su comportamiento interno y facilitar la creación de señuelos creíbles.

- **Técnicas ATT&CK utilizadas:**
  - T1566.001 – Phishing: Spearphishing Attachment  
    https://attack.mitre.org/techniques/T1566/001/
  - CWE-598 – Information Exposure Through Query Strings in GET Request  
    https://cwe.mitre.org/data/definitions/598.html

### 3️⃣ Delivery (Entrega del Ataque)

Distribuir los correos usando SMTP anónimo y campañas dirigidas. Esperar que uno de los operadores caiga en el señuelo y entregue sus credenciales. Esto permitira obtener un token JWT válido para acceder al backend como usuario legítimo.

- **Técnicas ATT&CK utilizadas:**
  - T1566.002 – Phishing: Spearphishing Link  
    https://attack.mitre.org/techniques/T1566/002/

### 4️⃣ Exploitation (Explotación de la Vulnerabilidad)
Ingresar con el JWT al sistema, modificar umbrales de ruido y alterar registros históricos. Si no se tiene firma digital ni validación de origen en los datos se pude inyectar eventos falsos que simulaban problemas acústicos graves, especialmente en instituciones clave.

- **Técnicas ATT&CK utilizadas:**
  - T1078 – Valid Accounts  
    https://attack.mitre.org/techniques/T1078/
  - T1565.001 – Data Manipulation: Stored Data Manipulation  
    https://attack.mitre.org/techniques/T1565/001/
- **CWE relevante:**
  - CWE-345 – Insufficient Verification of Data Authenticity  
    https://cwe.mitre.org/data/definitions/345.html


### 5️⃣ Installation (Persistencia)
Instalar una web shell PHP en el servidor backend usando una vulnerabilidad en la API. Luego programar tareas periódicas (cron) para restaurar la puerta trasera y asegurarme de que sobreviviera a reinicios o intentos de remediación.

- **Técnicas ATT&CK utilizadas:**
  - T1053.003 – Scheduled Task/Job: Cron  
    https://attack.mitre.org/techniques/T1053/003/
  - T1505.003 – Server Software Component: Web Shell  
    https://attack.mitre.org/techniques/T1505/003/

### 6️⃣ Command & Control (C2)

Desde un servidor externo bajo mi control, establecer un canal HTTPS para monitorear la operación, subir scripts y automatizar comandos maliciosos. Usar scripts en Python para mantener sincronización entre la shell remota y el entorno C2

- **Técnicas ATT&CK utilizadas:**
  - T1071.001 – Application Layer Protocol: Web Protocols (HTTPS)  
    https://attack.mitre.org/techniques/T1071/001/
  - T1105 – Ingress Tool Transfer  
    https://attack.mitre.org/techniques/T1105/


### 7️⃣ Actions on Objectives (Acción sobre el Objetivo)

Alterar los dashboards públicos del sistema para mostrar datos alarmantes falsos. En paralelo enviar comunicaciones anónimas sugiriendo que los registros de audio podrían publicarse si no se realizaba un pago. Esto dañó la reputación del sistema e inutilizar en ambientes críticos.

- **Técnicas ATT&CK utilizadas:**
  - T1491.001 – Defacement: Internal Defacement  
    https://attack.mitre.org/techniques/T1491/001/
  - T1565.002 – Data Manipulation: Transmitted Data Manipulation  
    https://attack.mitre.org/techniques/T1565/002/

## Diagrama de Flujo del Ataque
(El diagrama se incluirá en una versión posterior en formato gráfico)

1. [Reconocimiento: OSINT + Infraestructura]
         ⬇
2. [Phishing dirigido: clonado del dashboard]
         ⬇
3. [Captura de credenciales y JWT]
         ⬇
4. [Modificación de datos y registros]
         ⬇
5. [Persistencia: webshell y cronjob]
         ⬇
6. [C2 cifrado desde servidor externo]
         ⬇
7. [Sabotaje + extorsión + exfiltración acústica]

## Conclusión
Este ataque revela la importancia de implementar autenticación robusta, validación de datos, cifrado de extremo a extremo y control de acceso por niveles. A través de vulnerabilidades aparentemente menores, fue posible comprometer por completo un sistema crítico de monitoreo ambiental. La manipulación de datos acústicos no solo generó caos y desconfianza, sino que abrió la puerta a campañas de espionaje, sabotaje y extorsión. Un sistema confiable requiere una seguridad diseñada desde el inicio.

