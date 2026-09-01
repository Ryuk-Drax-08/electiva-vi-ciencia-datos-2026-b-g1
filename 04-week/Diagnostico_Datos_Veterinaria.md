# Diagnóstico de datos de un proceso

## Caso de estudio: gestión de citas en una clínica veterinaria

---

## 1. Descripción del proceso

El proceso seleccionado corresponde a la **gestión de citas de una clínica veterinaria**. Una clínica debe administrar diariamente consultas generales, vacunación, controles médicos, procedimientos, cirugías y otros servicios. Uno de los problemas que puede afectar su operación es la **inasistencia de clientes a las citas programadas**, las cancelaciones realizadas con poca anticipación y los espacios libres que quedan en la agenda.

Esta situación puede generar pérdida de capacidad operativa, reducción de ingresos, tiempos improductivos para los profesionales veterinarios y mayores tiempos de espera para otros clientes que sí necesitan atención.

El objetivo del análisis de datos será identificar patrones asociados con las cancelaciones y las inasistencias para apoyar una mejor gestión de la agenda.

---

## 2. Problema de negocio y pregunta de datos

La clínica necesita conocer qué factores están relacionados con la probabilidad de que una cita sea cancelada o que el cliente no se presente.

La información puede encontrarse distribuida entre el sistema de citas, las historias clínicas, los registros de clientes, las comunicaciones por WhatsApp, los pagos y otros medios.

Analizar estos datos permitiría comprender el comportamiento de las citas y, posteriormente, desarrollar mecanismos para anticipar posibles inasistencias.

### Pregunta de datos

> **¿Qué factores relacionados con el cliente, la mascota, el tipo de servicio, el horario y el historial de citas permiten identificar o predecir el riesgo de cancelación o inasistencia a una cita veterinaria?**

### 2.1 Preguntas secundarias

- ¿Qué días y horarios presentan mayor porcentaje de inasistencia?
- ¿Qué servicios veterinarios presentan más cancelaciones?
- ¿Los clientes con inasistencias anteriores tienen mayor probabilidad de volver a faltar?
- ¿Existe relación entre la anticipación con la que se agenda una cita y la inasistencia?
- ¿Los recordatorios enviados antes de la cita disminuyen el número de ausencias?
- ¿Qué segmentos de clientes presentan mayor nivel de cumplimiento?

---

## 3. Objetivos

### 3.1 Objetivo general

Analizar los datos generados durante el proceso de gestión de citas de una clínica veterinaria para identificar patrones asociados con cancelaciones e inasistencias y apoyar la optimización de la agenda.

### 3.2 Objetivos específicos

1. Consolidar las principales fuentes de información relacionadas con las citas.
2. Identificar problemas de calidad, valores faltantes, duplicados e inconsistencias.
3. Analizar la frecuencia de cancelaciones e inasistencias.
4. Identificar variables asociadas con estos comportamientos.
5. Determinar la posibilidad de construir posteriormente un modelo predictivo.
6. Generar información útil para mejorar la planificación de la agenda.

---

## 4. Inventario de datos

Para responder la pregunta de datos se requieren diferentes fuentes de información. Se proponen **nueve fuentes**, superando el mínimo de seis exigido en la actividad.

| # | Fuente | Campos / información relevante | Tipo de dato | Justificación |
|---:|---|---|---|---|
| 1 | Sistema de citas | ID de cita, fecha, hora, estado, fecha de creación | **Estructurado** | Campos definidos dentro de tablas. |
| 2 | Registro de clientes | ID cliente, edad aproximada, ciudad, citas anteriores | **Estructurado** | Atributos con formato y estructura definidos. |
| 3 | Registro de mascotas | ID mascota, especie, raza, edad, sexo | **Estructurado** | Puede almacenarse mediante columnas en una base de datos. |
| 4 | Servicios veterinarios | Tipo de consulta, procedimiento, duración y precio | **Estructurado** | Categorías y valores previamente definidos. |
| 5 | Historial de citas | Citas cumplidas, canceladas y no atendidas | **Estructurado** | Eventos relacionables mediante IDs y fechas. |
| 6 | Mensajes de WhatsApp | Confirmaciones, cancelaciones y respuestas | **Semiestructurado** | Tienen metadatos, pero el contenido es texto libre. |
| 7 | Logs del sistema | Fecha, usuario, acción, evento y código | **Semiestructurado** | Presentan patrones y etiquetas sin ser necesariamente tabulares. |
| 8 | Observaciones del veterinario | Notas sobre la consulta o el comportamiento del cliente | **No estructurado** | Corresponden principalmente a texto libre. |
| 9 | Documentos o adjuntos | Fotografías, fórmulas, certificados o PDF | **No estructurado** | Contienen texto o imágenes sin estructura uniforme. |

---

## 5. Clasificación general de los datos

### 5.1 Datos estructurados

Son aquellos que tienen una estructura definida y pueden almacenarse fácilmente en tablas de una base de datos.

En este proyecto corresponden principalmente a:

- citas;
- clientes;
- mascotas;
- servicios;
- historial de asistencia.

Ejemplo:

```text
appointment_id = 1052
client_id = 458
pet_id = 753
date = 2026-08-20
time = 14:30
service = Vaccination
status = Completed
```

### 5.2 Datos semiestructurados

Tienen cierto nivel de organización mediante campos, etiquetas o metadatos, pero su contenido no necesariamente sigue una estructura tabular.

Ejemplos:

- mensajes de WhatsApp;
- notificaciones;
- logs del sistema;
- respuestas de API.

Ejemplo:

```json
{
  "message_id": 503,
  "client_id": 458,
  "channel": "WhatsApp",
  "message": "No puedo asistir mañana, necesito cambiar la cita."
}
```

### 5.3 Datos no estructurados

No siguen un modelo de datos previamente definido.

En este caso pueden encontrarse:

- observaciones escritas por el veterinario;
- documentos PDF;
- fotografías;
- imágenes clínicas.

Para analizarlos podrían requerirse técnicas adicionales de **procesamiento de lenguaje natural** o **visión por computador**.

---

## 6. Variables principales para el análisis

| Variable | Descripción |
|---|---|
| `appointment_status` | Cita cumplida, cancelada o inasistencia |
| `appointment_day` | Día de la semana |
| `appointment_hour` | Hora de la cita |
| `service_type` | Tipo de servicio veterinario |
| `booking_days` | Días entre la creación y realización de la cita |
| `previous_appointments` | Número de citas anteriores |
| `previous_no_shows` | Número de inasistencias anteriores |
| `reminder_sent` | Indica si se envió recordatorio |
| `reminder_confirmed` | Indica si el cliente confirmó |
| `pet_species` | Especie de la mascota |
| `pet_age` | Edad de la mascota |
| `client_zone` | Zona o municipio del cliente |

### Variable objetivo propuesta

La variable objetivo propuesta sería:

```text
no_show
```

Donde:

```text
0 = cliente asistió
1 = cliente no asistió
```

---

## 7. Tipo de analítica

El proyecto puede recorrer diferentes niveles de analítica. El enfoque principal propuesto es **predictivo**, pero depende de una etapa previa descriptiva y diagnóstica, y puede evolucionar hacia decisiones prescriptivas.

### 7.1 Analítica descriptiva — ¿Qué está pasando?

Permite calcular indicadores como:

- cantidad total de citas;
- porcentaje de citas atendidas;
- porcentaje de cancelaciones;
- porcentaje de inasistencias;
- servicios con mayor demanda;
- horarios con mayor ocupación.

Ejemplo:

```text
Total de citas: 1.000
No-show: 90
Tasa de inasistencia: 9 %
```

### 7.2 Analítica diagnóstica — ¿Por qué está pasando?

Busca relaciones entre la inasistencia y variables como:

- día de la semana;
- hora;
- servicio;
- historial del cliente;
- anticipación de la reserva;
- existencia de recordatorios.

El objetivo es identificar qué factores están asociados con una mayor o menor probabilidad de inasistencia.

### 7.3 Analítica predictiva — ¿Qué probablemente ocurrirá?

Este es el **tipo principal de analítica propuesto**.

Con suficientes datos históricos se podría entrenar un modelo que estime la probabilidad de inasistencia de cada nueva cita.

Entre los algoritmos que podrían evaluarse se encuentran:

- Logistic Regression;
- Decision Tree;
- Random Forest;
- Gradient Boosting.

Ejemplo conceptual:

```text
Cita: 2058
Probabilidad de inasistencia: 82 %
Riesgo: ALTO
```

### 7.4 Analítica prescriptiva — ¿Qué deberíamos hacer?

A partir del riesgo estimado, el sistema podría recomendar diferentes acciones.

Por ejemplo:

```text
Riesgo bajo
→ Recordatorio estándar

Riesgo medio
→ Recordatorio + solicitud de confirmación

Riesgo alto
→ Confirmación anticipada + contacto con el cliente
```

También podría recomendar utilizar una **lista de espera** para ocupar rápidamente los espacios liberados.

### 7.5 Tipo de analítica seleccionado

Aunque el proyecto comienza necesariamente con analítica descriptiva y diagnóstica, el objetivo principal es llegar a una **analítica predictiva**, porque la pregunta busca identificar los factores que permitan estimar el riesgo de cancelación o inasistencia.

Posteriormente, las predicciones podrían utilizarse como entrada para una capa de **analítica prescriptiva**.

```text
DESCRIPTIVA → DIAGNÓSTICA → PREDICTIVA → PRESCRIPTIVA
```

---

## 8. ¿Es un caso de Big Data?

En el contexto inicial de una clínica veterinaria pequeña o mediana, el caso **no se considera todavía un problema de Big Data**.

Esta conclusión se justifica mediante las cinco V de Big Data.

| V | Evaluación del caso | Nivel |
|---|---|---|
| **Volume** | Una clínica puede producir miles de registros, pero todavía pueden gestionarse con bases de datos tradicionales. | Bajo / Medio |
| **Velocity** | Las citas y mensajes se generan durante el día, pero no existe un flujo de millones de eventos por segundo. | Bajo |
| **Variety** | Existen tablas, mensajes, logs, documentos, imágenes y notas clínicas. | Alto |
| **Veracity** | Pueden existir datos incompletos, duplicados, errores de digitación o estados inconsistentes. | Medio / Alto |
| **Value** | Los datos pueden utilizarse para disminuir inasistencias, mejorar la agenda y apoyar decisiones. | Alto |

### Conclusión sobre Big Data

El caso presenta **variedad, veracidad y valor** relevantes, pero inicialmente no tiene el **volumen** ni la **velocidad** que justifiquen una plataforma específica de Big Data.

Una base de datos relacional y herramientas convencionales de análisis serían suficientes.

> **Por lo tanto, el caso no se clasifica inicialmente como Big Data.**

Si el sistema creciera a una red nacional de clínicas con millones de eventos, imágenes médicas y comunicaciones en tiempo real, podría evolucionar hacia un escenario de Big Data.

---

## 9. Ciclo de vida del proyecto de datos

El ciclo de vida se aplica directamente al caso, desde la formulación de la pregunta hasta la decisión operativa.

```mermaid
flowchart LR
    A["1. Pregunta<br/>¿Qué factores permiten identificar o predecir una inasistencia?"]
    B["2. Obtener<br/>Citas, clientes, mascotas, servicios, mensajes y logs"]
    C["3. Limpiar<br/>Duplicados, nulos, formatos, estados y errores"]
    D["4. Analizar<br/>Indicadores, patrones y relaciones"]
    E["5. Visualizar<br/>Gráficos, tasas y comparaciones"]
    F["6. Decidir<br/>Recordatorios, confirmaciones y optimización de agenda"]

    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
```

### 9.1 Pregunta

> ¿Qué factores permiten identificar o predecir el riesgo de inasistencia a una cita veterinaria?

### 9.2 Obtener

Se recopilan datos provenientes de:

- agenda de citas;
- clientes;
- mascotas;
- servicios;
- historial de asistencia;
- recordatorios;
- mensajes;
- logs.

### 9.3 Limpiar

Se revisan:

- registros duplicados;
- campos vacíos;
- fechas incorrectas;
- IDs inexistentes;
- estados equivalentes escritos de formas distintas;
- valores inconsistentes.

Por ejemplo:

```text
"No asistió"
"NO_SHOW"
"Ausente"
```

Estos valores deberían transformarse a una categoría estándar:

```text
NO_SHOW
```

### 9.4 Analizar

Se calculan indicadores como la **tasa de no-show** y se estudian relaciones entre las inasistencias y las diferentes variables disponibles.

Ejemplo:

```text
Tasa de no-show =
Número de citas no atendidas
----------------------------
Número total de citas
```

### 9.5 Visualizar

Los resultados pueden presentarse mediante:

- gráficos de citas por día;
- porcentaje de inasistencia por horario;
- inasistencias por servicio;
- tasa de cumplimiento por cliente;
- comparación entre citas con y sin recordatorio.

### 9.6 Decidir

Los resultados se convierten en acciones como:

- mejorar recordatorios;
- solicitar confirmación;
- utilizar listas de espera;
- identificar citas de mayor riesgo;
- redistribuir horarios de atención.

---

## 10. Problem & data

Veterinary clinics can lose time and resources when customers cancel appointments or do not attend their scheduled visits. The main data question is to identify which factors can help predict the risk of a customer missing an appointment. To perform the analysis, the clinic needs data about appointments, customers, pets, veterinary services, previous attendance, reminders and communication messages. Most appointment and customer information is structured, while WhatsApp messages and system logs can be considered semi-structured data, and veterinary notes or documents can be unstructured. Descriptive analytics can first be used to understand appointment patterns and measure the current no-show rate. Predictive analytics can then be applied to estimate the probability that a future appointment will not be attended. Finally, these results could support prescriptive actions such as sending additional reminders, requesting confirmation or improving the scheduling process.

---

## 11. Resultados esperados

1. Identificar qué datos están disponibles y cuáles hacen falta.
2. Detectar problemas de calidad y consistencia.
3. Calcular la tasa actual de inasistencias.
4. Determinar qué variables parecen relacionadas con cancelaciones o no-shows.
5. Establecer si existen suficientes datos para desarrollar posteriormente un modelo predictivo.
6. Definir decisiones operativas que puedan apoyarse con los resultados.

---

## 12. Conclusión

El proceso de gestión de citas veterinarias genera diferentes tipos de datos que pueden aprovecharse para mejorar la operación de una clínica.

El diagnóstico permite identificar datos **estructurados, semiestructurados y no estructurados**, así como evaluar su utilidad y calidad antes de desarrollar modelos más avanzados.

Aunque el escenario inicial no requiere tecnologías de Big Data, existe suficiente variedad de información para realizar análisis descriptivos, diagnósticos y posteriormente predictivos.

El principal valor del proyecto consiste en transformar los datos históricos de las citas en información que permita comprender el comportamiento de los clientes y apoyar decisiones para reducir las inasistencias y utilizar de manera más eficiente la capacidad de atención de la clínica.

---

## Checklist de cumplimiento

- [x] Proceso o problema real definido.
- [x] Pregunta de datos clara y relevante.
- [x] Inventario con más de 6 fuentes/campos.
- [x] Datos clasificados como estructurados, semiestructurados y no estructurados.
- [x] Tipo de analítica definido y justificado.
- [x] Evaluación de Big Data mediante las 5 V.
- [x] Ciclo de vida aplicado al caso.
- [x] Sección `Problem & data` en inglés con mínimo 5 oraciones.
