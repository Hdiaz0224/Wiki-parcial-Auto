# 📦 Proyecto de Clasificación Automatizada de Cajas

## 1. Introducción
Este proyecto tiene como objetivo el diseño e implementación de un sistema automatizado de clasificación de cajas en una cinta transportadora, empleando **PLC y lenguaje Ladder** en CODESYS, con dos aproximaciones:

1. **Control por tiempo (TON):** clasificación basada en secuencia temporizada.  
2. **Control por sensores:** clasificación según la activación de sensores infrarrojos que determinan el tamaño de la caja.  

La solución fue validada a nivel de simulación en CODESYS y posteriormente se contempla su implementación en hardware real mediante OpenPLC y Arduino.

---

## 2. Etapas del Proyecto

### 2.1 Diseño
- **Análisis del proceso:** La cinta transportadora funciona continuamente mientras el sistema esté activo.  
- **Clasificación de cajas:**
  - Caja pequeña → solo sensor inferior activo → sigue derecho.  
  - Caja mediana → sensor inferior + medio activos → desvío por pistón 2.  
  - Caja grande → los tres sensores activos → desvío por pistón 1.  
  - Cualquier otra combinación → señal de error.  
- **Restricciones de diseño:**
  - El motor debe funcionar únicamente durante el proceso.  
  - Los pistones deben accionarse por un tiempo fijo (5 segundos).  
  - La señal de error detiene la operación y activa la luz de falla.  
- **Estándares aplicados:**
  - IEC 61131-3: estándar de programación para PLCs.  
  - ISO 12100: seguridad de máquinas (principios generales de diseño).  

### 2.2 Desarrollo
- Definición de variables (entradas, salidas, internos, temporizadores).  
- Elaboración de la lógica de control en Ladder (CODESYS).  
- Diseño del HMI en CODESYS para simular:
  - Botones (Start, Stop, Emergency).  
  - Luces piloto (RUN, FAULT, END).  
  - Sensores y pistones.  

### 2.3 Implementación
- Programación Ladder en CODESYS (se incluyen capturas de los rungs).  
- Integración de HMI en CODESYS.  
- Diagrama de circuito eléctrico (conexión de sensores infrarrojos y pistones a salidas digitales del PLC/Arduino).  

📷 *[Espacio para diagrama Ladder]*  
📷 *[Espacio para diagrama de conexiones eléctricas]*  
📷 *[Espacio para el HMI en CODESYS]*  

### 2.4 Validación
- **Simulación en CODESYS:** verificación del comportamiento esperado en los dos modos (tiempo y sensores).  
- **Prototipado en hardware real (Arduino + LEDs + sensores IR):** pendiente de implementación.  
- **Video demostrativo:** máximo 10 minutos con explicación y pruebas.  

📷 *[Espacio para fotos del prototipo con Arduino y LEDs]*  

---

## 3. Definición de Variables

| Nombre          | Tipo     | Atributo      | Descripción |
|-----------------|----------|---------------|-------------|
| `START_BTN`     | BOOL     | Entrada       | Botón de arranque |
| `STOP_BTN`      | BOOL     | Entrada       | Botón de parada normal |
| `EMERGENCY`     | BOOL     | Entrada       | Botón de emergencia |
| `FAULT`         | BOOL     | Entrada       | Señal de anomalía |
| `S1`, `S2`, `S3`| BOOL     | Entrada       | Sensores IR (inferior, medio, superior) |
| `MOTOR_CONVEYOR`| BOOL     | Salida        | Motor de la cinta transportadora |
| `PISTON1`       | BOOL     | Salida        | Actuador para cajas grandes |
| `PISTON2`       | BOOL     | Salida        | Actuador para cajas medianas |
| `L_RUN`         | BOOL     | Salida        | Luz verde (proceso en marcha) |
| `L_FAULT`       | BOOL     | Salida        | Luz roja (anomalía) |
| `TP1`, `TP2`    | TON      | Temporizador  | Temporizadores para pistones |
| `BIG_BOX`       | BOOL     | Interno       | Caja grande detectada |
| `MED_BOX`       | BOOL     | Interno       | Caja mediana detectada |
| `SMALL_BOX`     | BOOL     | Interno       | Caja pequeña detectada |
| `ERROR_BOX`     | BOOL     | Interno       | Caja en condición inválida |

---

## 4. Diagramas

- **Diagrama de actividades (secuencial):** flujo del proceso → Start → Motor ON → Lectura sensores → Clasificación → Pistón → Fin o Error.  
- **Diagrama eléctrico:** conexiones PLC ↔ sensores ↔ actuadores ↔ LEDs.  

📷 *[Espacio para diagrama de actividades]*  
📷 *[Espacio para diagrama eléctrico]*  

---

## 5. Roles y Contribuciones
- **Integrante A:** diseño del Ladder y definición de variables.  
- **Integrante B:** desarrollo del HMI en CODESYS y documentación.  
- **Integrante C:** validación con hardware real y elaboración del video.  

---

## 6. Uso de Inteligencia Artificial
Parte del borrador de la documentación fueron elaborados con apoyo de **ChatGPT (OpenAI, 2025)**. El contenido fue revisado, adaptado y complementado por el equipo.  

---

## 7. Bibliografía
1. IEC 61131-3: *Programmable Controllers – Part 3: Programming Languages*. International Electrotechnical Commission, 2013.  
2. ISO 12100:2010. *Safety of machinery — General principles for design — Risk assessment and risk reduction*.  
3. Bolton, W. (2015). *Programmable Logic Controllers*. 6th Edition. Newnes.  
4. Petruzella, F. D. (2016). *Programmable Logic Controllers*. McGraw Hill.  
5. OpenPLC Project. *Official Documentation*. [https://www.openplcproject.com](https://www.openplcproject.com)  
6. CODESYS Group. *CODESYS Documentation Portal*. [https://help.codesys.com](https://help.codesys.com)  
