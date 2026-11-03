# Telelol | LabEnvironment

Laboratorio virtual **didáctico** de variables ambientales. Mide la temperatura y el gas de un recinto cerrado con un Arduino Uno, limpia las señales, y compara lo medido con el modelo físico teórico, explicando cada paso para quien no tiene conocimientos previos.

**Demo:** https://TU-USUARIO.github.io/telelol/

> Proyecto Innovación Tecnológica · Prof. Ana G. Jorquera F.
> Temática: Calor y Ambiente / Fluidos
> Público objetivo: estudiantes de 2° año de Ingeniería Civil y de cursos iniciales de Termodinámica y Transferencia de Calor.

## Integrantes

- Javier González
- Eduardo Valenzuela
- Diego Cariz
- Gaspar Lineros
- Camilo Gómez

## ¿Qué se puede hacer?

La aplicación tiene 3 pantallas guiadas:

| Pantalla | Qué hace |
|---|---|
| **1. Configurar** | Conecta un Arduino real (Web Serial) o activa el simulador, define los parámetros del experimento y muestra el estado de los sensores. |
| **2. Medir** | Ejecuta el ensayo en tiempo real: circuito animado, estado del aire, dos gráficos en vivo y explicaciones de lo que ocurre en cada etapa. |
| **3. Comparar** | Superpone la curva medida con la teórica, calcula estadísticas, interpreta el resultado y exporta los datos a CSV. |

Cada parámetro tiene un tooltip **(?)** con una explicación sencilla.

## Cómo usarlo

1. Abre la página (o `index.html`) en **Chrome o Edge**.
2. En **Configurar**, elige **Simulador** o **Arduino real** y pulsa **Conectar**.
3. Revisa o cambia los parámetros del experimento.
4. En **Medir**, pulsa **Iniciar ensayo**. Los primeros 5 segundos deben ser en **aire limpio**, porque ahí se calibra el sensor de gas.
5. Al terminar, la app pasa sola a **Comparar**. Ahí puedes leer la interpretación y pulsar **Exportar a CSV**.

## Hardware

| Componente | Pin | Función |
|---|---|---|
| Arduino Uno | — | Lee los sensores y envía datos por USB |
| TMP36 | A0 | Temperatura |
| MQ-2 | A1 | Gas combustible y humo |

El Arduino debe enviar **una línea JSON por segundo** a 9600 baud, con al menos estos dos campos:

```json
{"temperatura": 24.50, "nivel_gases_contaminantes": 312.0}
```

- `temperatura`: temperatura en °C.
- `nivel_gases_contaminantes`: lectura ADC del MQ-2 (0 a 1023).

> Si el firmware ya promedia las lecturas antes de enviarlas, el filtro de la web se aplica por segunda vez. Para que el filtrado sea solo del cliente, el Arduino debe enviar las lecturas sin promediar.

## Procesamiento de señal

**Temperatura (TMP36):** `T(°C) = (Vout − 0,5) × 100`

**Gas (MQ-2):** el voltaje se convierte en la resistencia del sensor `Rs`. Se calcula `R0` con los primeros 5 s en aire limpio y los ppm salen de la curva de calibración:

```
ppm = a · (Rs / R0)^b
```

Las constantes `a` y `b` son **aproximaciones** de las curvas del datasheet para GLP, humo e hidrógeno.

**Filtro de media móvil de 5 muestras:**

```
y[n] = (x[n] + x[n−1] + x[n−2] + x[n−3] + x[n−4]) / 5
```

## Modelo físico

Acumulación de gas en un recinto cerrado:

```
C(t) = Cmax · (1 − e^(−t/τc))
```

Calentamiento (ley de Newton):

```
T(t) = Tamb + (Tmax − Tamb) · (1 − e^(−t/τt))
```

## Análisis estadístico

La pantalla **Comparar** calcula, para gas y temperatura:

- Promedio (X̄) y desviación estándar (σ).
- Error relativo porcentual punto a punto: `Er% = |(Xexp − Xteo) / Xteo| × 100`.
- Una frase que interpreta qué tan bien coincidió el experimento con la teoría.

Se omiten los puntos donde la teoría vale 0. La temperatura se calcula en kelvin para no dividir por cero.

## Exportación

El CSV incluye estas columnas:

```
t_s, V_raw_V, C_exp_ppm, T_exp_C, C_fil_ppm, T_fil_C, C_teo_ppm, T_teo_C
```

## Limitaciones

- La conversión del MQ-2 a ppm usa constantes aproximadas y debe verificarse con el datasheet del sensor usado.
- El error porcentual del gas suele ser mayor al inicio del ensayo, porque el filtro retrasa la curva y la teoría vale casi 0.
- El volumen del recinto se guarda en el resumen, pero no cambia los cálculos.
- En modo simulación los datos son virtuales: reproducen el modelo teórico con ruido, no mediciones reales.
- Necesita internet para cargar Chart.js y las fuentes, y Web Serial solo funciona en Chrome y Edge.

## Tecnologías

HTML5 · CSS3 · JavaScript ES6 · Chart.js · Web Serial API · Arduino (C++)

## Estructura del repositorio

```
index.html   → aplicación completa (CSS, JS y foto del circuito incluidos)
README.md    → este archivo
```
