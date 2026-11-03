# Telelol | LabEnvironment

Laboratorio virtual de **variables ambientales**: mide la temperatura y el gas de un recinto cerrado con un Arduino Uno y compara lo medido con el modelo teórico.

**Demo:** [https://TU-USUARIO.github.io/telelol/](https://24gsprrr.github.io/)

> Proyecto Innovación Tecnológica · Prof. Ana G. Jorquera F.
> Temática: Calor y Ambiente / Fluidos

## Integrantes

- Javier González
- Eduardo Valenzuela
- Diego Cariz
- Gaspar Lineros
- Camilo Gómez

## ¿Qué hace?

Un Arduino con dos sensores vigila el aire de una habitación cerrada y avisa si hay demasiado gas. La página muestra el circuito funcionando, dos gráficos en vivo (temperatura y gas) y un resumen del resultado en lenguaje simple.

Se puede usar de dos formas:

- **Simulación:** no necesita hardware. Un Arduino virtual replica la lógica del código real.
- **Arduino real:** se conecta por USB usando la Web Serial API.

## Cómo usarlo

1. Abre la demo (o el archivo `index.html`) en **Chrome o Edge**.
2. Ajusta la fuente de calor y la fuente de gas.
3. Pulsa **Iniciar** y observa el circuito, los valores y las curvas.
4. Al terminar, revisa el resultado y descarga los datos con **Descargar datos** (CSV).

Para usar el Arduino real, pulsa **Usar mi Arduino real**, elige el puerto (9600 baud) y luego **Iniciar**.

## Hardware

| Componente | Pin | Función |
|---|---|---|
| Arduino Uno | — | Lectura y envío de datos por puerto serie |
| TMP36 | A0 | Temperatura |
| MQ-2 | A1 | Gas combustible y humo |

El Arduino envía una línea JSON por segundo:

```json
{"temperatura": 24.50, "nivel_gases_contaminantes": 312.0, "estado_aire": "Aire no Contaminado"}
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

Conversión del TMP36: `T(°C) = (Vout − 0.5) × 100`

Filtro de media móvil de 5 muestras: `y[n] = (x[n] + x[n−1] + x[n−2] + x[n−3] + x[n−4]) / 5`

Error relativo: `Er% = |(Xexp − Xteo) / Xteo| × 100`

## Limitaciones

- El MQ-2 entrega un voltaje, no ppm directos. La conversión a ppm de esta versión es **aproximada** y no usa la curva Rs/R0 del datasheet.
- Los gráficos y las fuentes se cargan desde internet (Chart.js y Google Fonts), por lo que se necesita conexión.
- Web Serial solo funciona en Chrome y Edge.

## Tecnologías

HTML5 · CSS3 · JavaScript ES6 · Chart.js · Web Serial API · Arduino (C++)

## Estructura del repositorio

```
index.html   → aplicación completa (CSS, JS y foto del circuito incluidos)
README.md    → este archivo
```
