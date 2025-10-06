#  Proyecto de Clasificación Automatizada de Cajas

## 1. Introducción
Este proyecto tiene como objetivo el diseño e implementación de un sistema automatizado de clasificación de cajas en una cinta transportadora, empleando **PLC y lenguaje Ladder** en CODESYS, con dos aproximaciones:

1. **Control por tiempo (TON):** clasificación basada en secuencia temporizada.  
2. **Control por sensores:** clasificación según la activación de sensores infrarrojos que determinan el tamaño de la caja.  

La solución fue validada a nivel de simulación en CODESYS y posteriormente su implementación en hardware real mediante OpenPLC y Arduino.

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
<img width="747" height="542" alt="image" src="https://github.com/user-attachments/assets/d0489935-8fec-4c2b-b5ab-513ce118a563" />


<img width="969" height="435" alt="image" src="https://github.com/user-attachments/assets/497f792d-d8b0-4cbd-81e8-cee570c67afa" />


#  Implementación en OpenPLC

##  Definición de variables
En OpenPLC se definieron las siguientes entradas, salidas y marcas internas:

| Nombre        | Tipo  | Atributo  | Dirección | Descripción |
|---------------|-------|-----------|-----------|-------------|
| SensorCaja0   | BOOL  | Entrada   | %IX0.0    | Sensor inferior de la caja |
| SensorCaja1   | BOOL  | Entrada   | %IX0.1    | Sensor medio de la caja |
| SensorCaja2   | BOOL  | Entrada   | %IX0.2    | Sensor superior de la caja |
| Start         | BOOL  | Entrada   | %IX0.3    | Botón de arranque |
| Stop          | BOOL  | Entrada   | %IX0.4    | Botón de parada |
| Emergency     | BOOL  | Entrada   | %IX0.5    | Botón de emergencia |
| MotorOut      | BOOL  | Salida    | %QX0.0    | Motor de la cinta transportadora |
| PistonOut1    | BOOL  | Salida    | %QX0.1    | Pistón de clasificación de cajas grandes |
| PistonOut2    | BOOL  | Salida    | %QX0.2    | Pistón de clasificación de cajas medianas |
| LedStart      | BOOL  | Salida    | %QX0.3    | Luz verde: proceso en marcha |
| LedStop       | BOOL  | Salida    | %QX0.4    | Luz amarilla: proceso detenido |
| LedEmergency  | BOOL  | Salida    | %QX0.5    | Luz roja: emergencia/falla |
| ProcessStart  | BOOL  | Interna   | %QX0.6    | Marca de enclavamiento de proceso |
| SR0           | SR    | Interna   | -         | Set/Reset de enclavamiento |

---
<img width="1280" height="1131" alt="image" src="https://github.com/user-attachments/assets/b2d100d8-98ca-451d-8b09-a21941c25f8d" />


##  Lógica Ladder en OpenPLC

La lógica desarrollada corresponde a un **sistema de clasificación de cajas**:

1. **Arranque y enclavamiento**  
   - `Start` activa `ProcessStart`, que mantiene el sistema en marcha hasta que se presione `Stop` o `Emergency`.

2. **Funcionamiento normal**  
   - Si `ProcessStart` está activo → se encienden el **motor de la cinta** (`MotorOut`) y la **luz verde** (`LedStart`).

3. **Clasificación de cajas según sensores**  
   - **Caja grande** → si se activan los tres sensores (`SensorCaja0`, `SensorCaja1`, `SensorCaja2`), se activa `PistonOut1`.  
   - **Caja mediana** → si se activan los sensores inferior y medio (`SensorCaja1` y `SensorCaja2`), se activa `PistonOut2`.  
   - **Caja pequeña** → solo el sensor inferior (`SensorCaja0`) está activo → la caja sigue sin desvío.  

4. **Condiciones de error**  
   - Combinaciones no válidas (ejemplo: sensores superior e inferior activos sin el medio, o solo el sensor medio activo) → activan `LedEmergency`.

5. **Parada del sistema**  
   - Al presionar `Stop` o `Emergency`, se resetea `ProcessStart`, se apaga el motor y se enciende la **luz amarilla** (`LedStop`).

---

##  Funcionamiento esperado
- El operador pulsa **Start** → motor en marcha + luz verde encendida.  
- Caja detectada por sensores → dependiendo del patrón de activación se clasificará como **grande** o **mediana** (activando pistones).  
- Caja pequeña → sigue derecho sin desvío.  
- Fallas de sensores → se enciende la luz roja (LedEmergency).  
- Botón **Stop** → parada normal con luz amarilla.  
- Botón **Emergency** → parada inmediata con luz roja.  

---



### 2.4 Validación
- **Simulación en OPEN PLC:** verificación del comportamiento esperado en los dos modos (tiempo y sensores).  

### 2.5 Prototipado

#### Prototipo físico de la banda transportadora

El proyecto de automatización y control de procesos consiste en una banda transportadora elaborada a partir de materiales reciclados y de bajo costo. La estructura está construida con cartón proveniente de una caja de la tienda D1, adaptada y cortada en forma de caja rectangular para alojar el sistema completo. 

El eje principal, encargado del movimiento, está hecho con un chazo que se conecta directamente al servomotor y está recubierto con plástico proveniente del centro de una bolsa para perros, lo que ayuda a reducir la fricción. El eje secundario, que es independiente, está construido con un tornillo rodeado también por el mismo tipo de plástico para permitir un giro libre. 

En la parte media se ubicó un tornillo adicional que cumple la función de ajustar la tensión de la banda, evitando que se deslice o se afloje durante el funcionamiento. La banda fue confeccionada con un trapo de cocina cosido con hilo, lo cual le da resistencia y cierta flexibilidad. 

Para mantener la estructura estable, especialmente por el peso del servomotor que está anclado en uno de los extremos, se añadieron patas de cartón que elevan y equilibran la banda transportadora, permitiendo un funcionamiento más estable y continuo.

IMPORTANTE

Se escogió el tamaño original basados en lo que nos permitiera trabajar con unos ejes que permitieran el movimiento de la banda transportadora. Como se escogieron los plásticos del centro de las bolsas para perros como nuestros ejes, las dimensiones del proyecto se acomodan a dichas medidas. Pensando en lo anterior, se establece que el proyecto cuenta con un radio de 7:1 de lo que se esperaría tener en un modelo real que trabaje con cajas de tamaño promedio con productos reales.

### Diseño de planos del prototipo

![Imagen de WhatsApp 2025-10-06 a las 06 41 34_f22eef25](https://github.com/user-attachments/assets/08bfdaa7-ad03-4dff-86bc-2807ee4cdf0f)

![Imagen de WhatsApp 2025-10-06 a las 06 41 34_324cded4](https://github.com/user-attachments/assets/8c5c61b4-801a-40da-82db-d1152af3d473)

![Imagen de WhatsApp 2025-10-06 a las 06 41 34_f473545c](https://github.com/user-attachments/assets/246c8552-b71e-45e6-9a38-37988096ad80)

### Protitipo final junto a cajas

![Imagen de WhatsApp 2025-10-06 a las 06 41 33_9daf54dd](https://github.com/user-attachments/assets/b590ff0f-528e-4414-a29f-011eaec58845)


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

- **Diagrama eléctrico:** conexiones PLC ↔ sensores ↔ actuadores ↔ LEDs.  

<img width="868" height="883" alt="image" src="https://github.com/user-attachments/assets/57d14c09-bc07-4611-a255-bf81bdb82208" />

* Para desarrollar el diagrama mostrado anteriormente se utilizó ClaudeAI 

---

## 5. Roles y Contribuciones
- **Hector Diaz y Cristian Rosero** diseño del Ladder y definición de variables.  
- **Hector Diaz** desarrollo del HMI en CODESYS y documentación.
- **Cristian Rosero:** validación con OPENPLC.
- **Cristian Rosero y Juan Pinilla:** validación con hardware real.
- **Cristian Rosero, Hector Diaz y Juan Pinilla** Creacion Wiki y creacion video
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
7. https://youtu.be/Aeb6ZOtcsp4?si=HfKpL-IPVeWncNhd
8. https://www.youtube.com/watch?v=UsF5Isjdgw4


