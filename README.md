<p align="center"><img src= img/portada.png width="100%" height="270"></p>

# Proyecto SQL: Análisis de atenciones del Hospital Management Dataset

## Resumen (Overview)

El director general del hospital  Management Dataset desea obtener una visión clara de las principales operaciones de la institución. Sin embargo, necesita información consolidada que le permita conocer el comportamiento de los pacientes y las atenciones realizadas, así como identificar los principales motivos de visita y el estado de las citas.

Mi objetivo es utilizar SQL para analizar los datos de gestión hospitalaria y proporcionar información que facilite el análisis de las principales operaciones del hospital, considerando los datos de pacientes y citas registrados en la base de datos

## Estructura del proyecto

- [Base de datos](#base-de-datos)
- [Tareas](#tareas)
- [Limpieza y reparación de datos](#limpieza-y-reparación-de-datos)
- [Análisis exploratorio de datos](#análisis-exploratorio-de-datos)
- [Conclusiones](#conclusiones)

## Base de datos

Los datos originales se pueden encontrar [aquí](https://www.kaggle.com/datasets/kanakbaghel/hospital-management-dataset?select=appointments.csv).

Creé una base de datos denominada `hospital_md`, compuesta por cinco tablas relacionadas entre sí: `patients`, `doctors`, `appointments`, `treatments y billing`. La estructura de los datos permite seguir el flujo de atención desde el registro del paciente y la programación de una cita, hasta la asignación del médico, la realización de tratamientos y la facturación correspondiente. En conjunto, la base de datos contiene más de 3,000 registros y 39 columnas.
A continuación, se presenta la estructura de las tablas y sus principales campos:

| Tabla | Descripción | Principales campos |
|---|---|---|
| `patients` | Almacena información demográfica y de contacto de los pacientes.| `patient_id`, `first_name`, `last_name`, `gender`, `date_of_birth` |
| `doctors` | Información de los médicos y sus especialidades. | `doctor_id`, `specialization`, `years_experience` |
| `appointments` | Registra las citas médicas y relaciona a los pacientes con los médicos. | `appointment_id`, `patient_id`, `doctor_id`, `appointment_date`, `status` |
| `treatments` | Registra los tratamientos y procedimientos realizados durante la atención médica. | `treatment_id`, `appointment_id`, `treatment_type`, `cost` |
| `billing` | Información de facturación y pagos. | `bill_id`, `patient_id`, `treatment_id`, `amount`, `payment_status` |

Las relaciones entre las tablas permiten integrar la información de las distintas etapas de la atención hospitalaria y realizar consultas SQL sobre los datos registrados.

## Tareas

Este análisis, ayudo al director del hospital a respoder las siguientes interrogantes:

1. ¿Cuántos pacientes están registrados en el hospital y cómo se distribuyen según el género?
2. ¿Cuántos pacientes se registraron durante cada año y cuál fue el año con mayor cantidad de nuevos registros?
3. ¿Cómo se distribuyen las citas según su estado y cuál es la cantidad de citas completadas, canceladas y no asistidas?
4. ¿Qué motivos de visita presentan el mayor porcentaje de citas no completadas?
5. ¿Qué médicos tienen la mayor carga de citas y cuál es su porcentaje de citas completadas?
6. ¿Qué especialidades concentran la mayor cantidad de citas y cuál es el promedio de años de experiencia de sus médicos?
7. ¿Qué tipos de tratamiento son los más frecuentes y cuál es su costo promedio?
8. ¿Qué tratamientos tienen un costo superior al costo promedio de todos los tratamientos registrados?
9. ¿Qué pacientes presentan una facturación acumulada superior al promedio de facturación por paciente?
10. ¿Cuál es el tratamiento de mayor costo dentro de cada tipo de tratamiento?
11. ¿Cómo se compara el tratamiento de mayor costo de cada tipo con el costo promedio de su respectiva categoría?
12. ¿Qué tipos de tratamiento concentran los mayores montos de facturación y cómo se distribuyen estos montos según el estado de pago?

## Limpieza y reparación de datos

Antes de realizar el análisis, se llevó a cabo una revisión de las cinco tablas de la base de datos con el propósito de verificar la calidad y consistencia de la información. 

### Valores nulos

Primero, se verifica los valores nulos en las tablas.

```sql
-- Verificar valores faltantes en la tabla Appointments --

SELECT *
FROM hospital_md.appointments
WHERE appointment_id IS NULL;

--Verificar valores faltantes en la tabla Patients--

SELECT *
FROM hospital_md.patients
WHERE patient_id IS NULL;

--Verificar valores faltantes en la tabla Doctors--

SELECT *
FROM hospital_md.doctors
WHERE doctor_id IS NULL;

--Verificar valores faltantes en la tabla Billing--

SELECT *
FROM hospital_md.billing
WHERE bill_id IS NULL;

--Verificar valores faltantes en la tabla Treatments--

SELECT *
FROM hospital_md.treatments
WHERE treatment_id IS NULL;
```
**Resultado** : No se encontraron valores nulos en las tablas

### Registros duplicados

A continuación, se verificó la existencia de registros duplicados en los identificadores principales de las cinco tablas.

```sql
-- Verificar valores duplicados en la tabla Appointments --

SELECT appointment_id, COUNT(*)
FROM hospital_md.appointments
GROUP BY appointment_id
HAVING COUNT(*) > 1;

-- Verificar valores duplicados en la tabla Patients --

SELECT patient_id, COUNT(*)
FROM hospital_md.patients
GROUP BY patient_id
HAVING COUNT(*) > 1;

-- Verificar valores duplicados en la tabla Doctors --

SELECT doctor_id, COUNT(*)
FROM hospital_md.doctors
GROUP BY doctor_id
HAVING COUNT(*) > 1;

-- Verificar valores duplicados en la tabla Billing --

SELECT bill_id, COUNT(*)
FROM hospital_md.billing
GROUP BY bill_id
HAVING COUNT(*) > 1;

-- Verificar valores duplicados en la tabla Treatments --

SELECT treatment_id, COUNT(*)
FROM hospital_md.treatments
GROUP BY treatment_id
HAVING COUNT(*) > 1;
```

**Resultado**: No se identificaron registros duplicados en los campos clave, por lo que los registros mantienen identificadores únicos.

## Análisis exploratorio de datos

### *Pregunta 1: ¿Cuántos pacientes están registrados en el hospital y cómo se distribuyen según el género?*

Encontré la cantidad de pacientes registrados y su distribución según el género utilizando las funciones `COUNT`, `GROUP BY` y `ORDER BY`. La función `COUNT(*)` permitió contabilizar los pacientes de cada género, mientras que `GROUP BY` agrupó los registros según el valor de la columna gender. Finalmente, `ORDER BY` permitió ordenar los resultados de mayor a menor cantidad de pacientes.

```sql
-- Distribución de pacientes según el género --

SELECT gender, COUNT(*) AS Total_Pacientes
FROM hospital_md.patients
GROUP BY gender
ORDER BY Total_Pacientes DESC;
```
<p align="center"><img src= img/pregunta1.png>

El análisis muestra que el hospital cuenta con **50 pacientes** registrados. De este total, los **hombres representan el 62%** de los registros, mientras que las **mujeres representan el 38%**.

`Decisión sugerida`: El director puede utilizar esta información como referencia para analizar posteriormente la demanda de citas y tratamientos según el género de los pacientes.

### *Pregunta 2: ¿Cuántos pacientes se registraron durante cada año y cuál fue el año con mayor cantidad de nuevos registros?*

Analicé la cantidad de pacientes registrados en cada año utilizando las funciones `COUNT`, `YEAR`, `GROUP BY` y `ORDER BY`. La función `YEAR` permitió extraer el año de la fecha de registro del paciente, mientras que `COUNT(*)` permitió contabilizar los registros correspondientes a cada año. Finalmente, `GROUP BY` agrupó los pacientes por año y `ORDER BY` permitió ordenar los resultados de mayor a menor cantidad de registros.

```sql
-- Cantidad de pacientes registrados por año --

SELECT 
    YEAR(registration_date) AS `Año`,
    COUNT(*) AS Total_Pacientes
FROM hospital_md.patients
GROUP BY YEAR(registration_date)
ORDER BY Total_Pacientes DESC;
```
<p align="center"><img src= img/pregunta2.png>

El análisis muestra que **2021** fue el año con **mayor cantidad de nuevos registros**, con **21 pacientes**, seguido de **2022** con **17 pacientes**. En total, se registraron 50 pacientes durante los años analizados.

`Decisión sugerida`: El director podría analizar las causas de la disminución en el número de nuevos registros entre 2021 y 2023, con el fin de identificar oportunidades para fortalecer la captación de pacientes mediante paquetes de servicios y ofertas dirigidas.

### *Pregunta 3: ¿Cómo se distribuyen las citas según su estado y cuál es la cantidad de citas completadas, canceladas y no asistidas?*

Para conocer el comportamiento de las citas, agrupé los registros según su estado utilizando `COUNT`, `GROUP BY` y `ORDER BY`. `COUNT(*)` permitió contabilizar las citas de cada categoría, mientras que `GROUP BY` las agrupó según su estado. Finalmente, `ORDER BY` organizó los resultados de mayor a menor cantidad de citas.

```sql
-- Distribución de citas según estado --

SELECT status, COUNT(*) AS Total_Citas
FROM hospital_md.appointments
GROUP BY status
ORDER BY Total_Citas DESC;
```

<p align="center"><img src= img/pregunta3.png>

En total, se analizaron **200 citas**. Las citas no asistidas representan el **26%** del total, mientras que las citas programadas y canceladas representan cada una el **25,5%** y las citas completadas el **23%**.

`Decisión sugerida`: El hospital podría implementar recordatorios previos a las citas, priorizando la reducción de las 52 inasistencias registradas, con el propósito de incrementar la cantidad de atenciones completadas.

### *Pregunta 4: ¿Qué motivos de visita presentan el mayor porcentaje de citas no completadas?*

Para esta pregunta vamos a analizar los motivos de visita con mayor proporción de citas no completadas, utilicé las funciones `COUNT`, `SUM`, `CASE`, `ROUND`, `GROUP BY`, `HAVING` y `ORDER BY`. La expresión `CASE` permitió identificar las citas que fueron canceladas o en las que el paciente no asistió, mientras que `SUM` permitió contabilizar estos casos. Posteriormente, se calculó el porcentaje de citas no completadas respecto al total de citas de cada motivo. Finalmente, `HAVING` permitió considerar únicamente los motivos que cuentan con al menos cinco citas y `ORDER BY` organizó los resultados de mayor a menor porcentaje.

```sql
-- Motivos de visita con mayor porcentaje de citas no completadas --

SELECT reason_for_visit, COUNT(*) AS Total_Citas, 
    SUM(CASE WHEN status IN ('Cancelled', 'No-show') THEN 1 ELSE 0 END ) AS Citas_No_Completadas, 
    ROUND( SUM( CASE WHEN status IN ('Cancelled', 'No-show') THEN 1 ELSE 0 END ) * 100.0 / COUNT(*), 2 ) AS Porcentaje_No_Completada

FROM hospital_md.appointments 
GROUP BY reason_for_visit
HAVING COUNT(*) >= 5 
ORDER BY Porcentaje_No_Completada DESC;
```

<p align="center"><img src= img/pregunta4.png>

Los resultados evidencian una mayor proporción de citas no completadas en **Emergency, Consultation y Therapy**, cuyos porcentajes superan el 59%. En contraste, **Checkup y Follow-up** presentan proporciones cercanas al 40%, mostrando una diferencia de más de 19 puntos porcentuales respecto a Emergency.

`Decisión sugerida`: Se recomienda priorizar la asignación de recursos de atención en Emergency, Consultation y Therapy, considerando que estos servicios presentan los mayores niveles de citas no completadas y, por tanto, un mayor volumen de atenciones que no llegan a concretarse.

### *Pregunta 5: ¿Qué médicos tienen la mayor carga de citas y cuál es su porcentaje de citas completadas?*

La consulta permitió identificar la cantidad de citas asignadas a cada médico y comparar este volumen con el porcentaje de citas que fueron completadas. Para ello, se relacionaron las tablas `doctors` y `appointments` mediante `JOIN`. Además, se utilizaron `COUNT` para contabilizar las citas, `CASE` y `SUM` para identificar las citas completadas y `ROUND` para calcular el porcentaje correspondiente.

```sql
-- Carga de citas y porcentaje de citas completadas por médico --

SELECT
    d.doctor_id,
    d.specialization,
    d.hospital_branch,
    COUNT(a.appointment_id) AS Total_Citas,
    SUM(CASE WHEN a.status = 'Completed' THEN 1 ELSE 0 END) AS Citas_Completadas,
        ROUND(SUM(CASE WHEN a.status = 'Completed' THEN 1 ELSE 0 END) * 100.0 / COUNT(a.appointment_id), 2) AS Porcentaje_Completadas
FROM hospital_md.doctors AS d
JOIN hospital_md.appointments AS a
    ON d.doctor_id = a.doctor_id
GROUP BY
    d.doctor_id,
    d.specialization,
    d.hospital_branch
ORDER BY Total_Citas DESC;
```
<p align="center"><img src= img/pregunta5.png>

La información evidencia diferencias importantes en el nivel de cumplimiento entre los médicos. El caso de **D005** destaca porque combina la mayor cantidad de citas asignadas con el menor porcentaje de citas completadas. En contraste, D007 registra la menor cantidad de citas, pero alcanza el mayor porcentaje de cumplimiento. Esto muestra que una **mayor carga de citas no se traduce necesariamente en una mayor cantidad proporcional de atenciones completadas**.

`Decisión sugerida`: Se podría redistribuir la carga de citas entre los médicos cuando existan diferencias marcadas entre el volumen asignado y el porcentaje de atenciones completadas, tomando como caso prioritario a D005 por concentrar la mayor cantidad de citas y presentar el menor nivel de cumplimiento

### *Pregunta 6: ¿Qué especialidades concentran la mayor cantidad de citas y cuál es el promedio de años de experiencia de sus médicos?*

La información obtenida permite comparar la cantidad de citas atendidas por cada especialidad con el promedio de años de experiencia de los médicos que pertenecen a ella. Para ello, se relacionaron las tablas `doctors` y`appointments` mediante `JOIN`. Se utilizaron `COUNT` para contabilizar las citas, `AVG` para calcular el promedio de experiencia y `ROUND` para presentar este valor con dos decimales. Finalmente, `GROUP BY` permitió agrupar los resultados por especialidad y `ORDER BY` organizó las especialidades según la cantidad de citas.

```sql
-- Citas y experiencia promedio por especialidad --

SELECT 
    d.specialization, 
    COUNT(a.appointment_id) AS Total_Citas, 
    COUNT(DISTINCT d.doctor_id) AS Total_Medicos, 
    ROUND(AVG(d.years_experience), 2) AS Promedio_Experiencia 
FROM hospital_md.doctors AS d 
JOIN hospital_md.appointments AS a 
    ON d.doctor_id = a.doctor_id 
GROUP BY d.specialization 
ORDER BY Total_Citas DESC;
```
<p align="center"><img src= img/pregunta6.png>

Pediatrics concentra la mayor cantidad de citas, con **98 registros distribuidos entre 5 médicos**, y presenta un promedio de **23,55 años de experiencia**. Le sigue Dermatology, con **70 citas entre 3 médicos** y un promedio de **17,99 años de experiencia**. Finalmente, Oncology registra **32 citas entre 2 médicos**, con un promedio de **23,03 años de experiencia**.

`Decisión sugerida`: Se debería priorizar la planificación de personal en Pediatrics, debido a que concentra la mayor cantidad de citas, y evaluar si la cantidad de médicos asignados es suficiente para atender esta demanda.

### *Pregunta 7: ¿Qué tipos de tratamiento son los más frecuentes y cuál es su costo promedio?*

La consulta permitió comparar la frecuencia de los diferentes tipos de tratamiento y su costo promedio. Para ello, se utilizaron las funciones `COUNT` para contabilizar los tratamientos y `AVG` para calcular el costo promedio. Asimismo, `GROUP BY` permitió agrupar los registros según el tipo de tratamiento, mientras que `ROUND` presentó los costos con dos decimales. Finalmente, `ORDER BY` organizó los resultados de acuerdo con la cantidad de tratamientos realizados.

```sql
-- Frecuencia y costo promedio por tipo de tratamiento --

SELECT 
    treatment_type, 
    COUNT(*) AS Total_Tratamientos, 
    ROUND(AVG(cost), 2) AS Costo_Promedio 
FROM hospital_md.treatments 
GROUP BY treatment_type 
ORDER BY Total_Tratamientos DESC;
```
<p align="center"><img src= img/pregunta7.png>

La **Chemotherapy** fue el tratamiento más frecuente, con **49 registros**, seguida de **X-Ray** con 41 y **ECG** con 38. Por otro lado, **MRI** presentó el mayor costo promedio, con **S/ 3,224.95**, aunque registró 36 tratamientos. **Chemotherapy** tuvo un costo promedio de **S/ 2,629.71**, mientras que **X-Ray**, **ECG** y **Physiotherapy** alcanzaron S/ 2,698.87, S/ 2,532.22 y S/ 2,761.61, respectivamente.

`Decisión sugerida`: Se recomienda considerar la frecuencia y el costo promedio de cada tratamiento para priorizar la planificación de recursos, especialmente en los tratamientos con mayor demanda y en aquellos que representan un mayor costo promedio, como MRI.

### *Pregunta 8: ¿Qué tratamientos tienen un costo superior al costo promedio de todos los tratamientos registrados?*

Esta pregunta nos permitirá comparar el costo de cada tratamiento con el **costo promedio general** de todos los tratamientos registrados. Para ello, utilizaremos una **subconsulta** para obtener el promedio general y luego filtraremos aquellos tratamientos cuyo costo sea superior a dicho valor.

La consulta puede resolverse con `AVG` para calcular el promedio general y una subconsulta dentro de `WHERE` para realizar la comparación.

```sql
-- Tratamientos con costo superior al promedio general --

SELECT treatment_id,treatment_type, cost
FROM hospital_md.treatments
WHERE cost > (
    SELECT AVG(cost)
    FROM hospital_md.treatments)
ORDER BY cost DESC;
```

<p align="center"><img src= img/pregunta8.png>

Entre los registros obtenidos, el tratamiento **T108**, correspondiente a **X-Ray**, presentó el mayor costo con **S/ 4,973.63**, seguido de **T130 (MRI)** con **S/ 4,966.18** y **T156 (Chemotherapy)** con **S/ 4,964.71**. También se identificaron tratamientos de **ECG** y **Physiotherapy** dentro de los registros con costos superiores al promedio general.

`Decisión sugerida`: El director podría establecer un seguimiento de los tratamientos cuyos costos superan el promedio general, revisando especialmente los registros con valores más elevados para identificar qué factores están asociados a estos mayores costos y mejorar el control de los gastos por tratamiento.

### *Pregunta 9: ¿Qué pacientes presentan una facturación acumulada superior al promedio de facturación por paciente?*

La consulta permitió identificar a los pacientes cuya facturación acumulada se encuentra por encima del promedio de facturación por paciente. Para ello, se utilizó `SUM` para acumular los montos facturados a cada paciente y `GROUP BY` para agrupar los registros por paciente. Además, se empleó una subconsulta para calcular el promedio de las facturaciones acumuladas y `HAVING` para seleccionar únicamente a los pacientes que superan dicho promedio. Finalmente, `ORDER BY` permitió ordenar los resultados de mayor a menor facturación.

```sql
-- Pacientes cuya facturación acumulada supera el promedio --

SELECT
    p.patient_id,
    p.first_name,
    p.last_name,
    SUM(b.amount) AS Facturacion_Acumulada
FROM hospital_md.patients AS p
JOIN hospital_md.billing AS b
    ON p.patient_id = b.patient_id
GROUP BY
    p.patient_id,
    p.first_name,
    p.last_name
HAVING SUM(b.amount) > (
    SELECT AVG(Facturacion_Paciente)
    FROM (
        SELECT
            patient_id,
            SUM(amount) AS Facturacion_Paciente
        FROM hospital_md.billing
        GROUP BY patient_id
    ) AS promedio_pacientes
)
ORDER BY Facturacion_Acumulada DESC;
```
<p align="center"><img src= img/pregunta9.png>

La consulta identificó **20 pacientes** cuya facturación acumulada supera el promedio registrado por paciente. **Laura Davis (P012)** presentó la mayor facturación acumulada, con **S/ 30,053.08**, seguida por **David Moore (P049)** con S/ 23,554.06 y **Michael Taylor (P016)** con S/ 22,967.94. En el extremo inferior del grupo identificado se encuentra **Michael Wilson (P032)**, con S/ 12,234.85.

`Decisión sugerida`: El hospital podría fortalecer el seguimiento de los servicios asociados a los pacientes con mayor facturación acumulada, con el objetivo de conocer qué atenciones concentran una mayor generación de ingresos.

### *Pregunta 10: ¿Cuál es el tratamiento de mayor costo dentro de cada tipo de tratamiento?*

La consulta permitió identificar el tratamiento con mayor costo dentro de cada tipo. Para ello, se utilizó `ROW_NUMBER()` como función de ventana y `PARTITION BY` para separar los tratamientos según su categoría. Luego, `ORDER BY cost DESC` ordenó los registros de mayor a menor costo dentro de cada grupo, asignando la posición 1 al tratamiento más costoso de cada tipo. Finalmente, `WHERE Posicion = 1` permitió obtener únicamente el registro de mayor costo de cada categoría.

```SQL
-- Tratamiento de mayor costo dentro de cada tipo --

WITH Tratamientos_Rankeados AS (
    SELECT
        treatment_id,
        treatment_type,
        cost,
        ROW_NUMBER() OVER (
            PARTITION BY treatment_type
            ORDER BY cost DESC
        ) AS Posicion
    FROM hospital_md.treatments
)
SELECT
    treatment_id,
    treatment_type,
    cost
FROM Tratamientos_Rankeados
WHERE Posicion = 1
ORDER BY cost DESC;
```
<p align="center"><img src= img/pregunta10.png>

El tratamiento de mayor costo fue **T108**, correspondiente a **X-Ray, con S/ 4,973.63**. Le siguieron **T130 (MRI)** con S/ 4,966.18, **T156 (Chemotherapy)** con S/ 4,964.71, **T083 (ECG)** con S/ 4,960.65 y **T192 (Physiotherapy)** con S/ 4,846.20. En todos los tipos de tratamiento se identificó un registro cuyo costo supera los S/ 4,800.

`Decisión sugerida`: Se podría utilizar los costos máximos identificados como valores de referencia para establecer rangos de costo por tipo de tratamiento, facilitando la planificación presupuestaria y la asignación de recursos para cada servicio.

### *Pregunta 11: ¿Cómo se compara el tratamiento de mayor costo de cada tipo con el costo promedio de su respectiva categoría?*

En esta consulta compararemos el tratamiento más costoso de cada categoría con el costo promedio de esa misma categoría. Para ello, primero se utilizará `AVG` para calcular el costo promedio por tipo y `ROW_NUMBER()` para identificar el tratamiento de mayor costo dentro de cada grupo. Finalmente, ambas consultas se relacionarán mediante `JOIN` para calcular cuánto supera el tratamiento más costoso al promedio de su categoría.

```SQL
-- Comparación entre el tratamiento más costoso y el promedio de su categoría --

WITH Promedios AS (
    SELECT
        treatment_type,
        AVG(cost) AS Costo_Promedio
    FROM hospital_md.treatments
    GROUP BY treatment_type
),

Tratamientos_Rankeados AS (
    SELECT
        treatment_id,
        treatment_type,
        cost,
        ROW_NUMBER() OVER (
            PARTITION BY treatment_type
            ORDER BY cost DESC
        ) AS Posicion
    FROM hospital_md.treatments
)

SELECT
    t.treatment_id,
    t.treatment_type,
    t.cost AS Costo_Maximo,
    ROUND(p.Costo_Promedio, 2) AS Costo_Promedio,
    ROUND(t.cost - p.Costo_Promedio, 2) AS Diferencia
FROM Tratamientos_Rankeados AS t
JOIN Promedios AS p
    ON t.treatment_type = p.treatment_type
WHERE t.Posicion = 1
ORDER BY Diferencia DESC;
```
<p align="center"><img src= img/pregunta11.png>

El tratamiento de mayor costo de **ECG** presentó la mayor diferencia respecto al promedio de su categoría, con **S/ 2,428.43**, seguido de **Chemotherapy** con S/ 2,335.00 y **X-Ray** con S/ 2,274.76. Por otro lado, **MRI** presentó la menor diferencia, con S/ 1,741.23, aunque registró el costo promedio más alto, de **S/ 3,224.95**.

`Decisión sugerida`: Se recomienda analizar las diferencias entre el costo máximo y el promedio de cada categoría para identificar los tipos de tratamiento con mayor variación de costos y evaluar si requieren criterios de tarifación diferenciados.

### *Pregunta 12: ¿Qué tipos de tratamiento concentran los mayores montos de facturación y cómo se distribuyen estos montos según el estado de pago?*

Esta pregunta permitirá relacionar los tratamientos con la información de facturación para identificar qué tipos de tratamiento generan los mayores montos y cómo se distribuyen según el estado de pago. Se utilizarán `JOIN` para relacionar las tablas, `SUM` para calcular los montos facturados y `CASE` para separar la facturación según el estado de pago. Finalmente, `GROUP BY` permitirá organizar los resultados por tipo de tratamiento.

```SQL
-- Facturación por tipo de tratamiento y estado de pago --

SELECT
    t.treatment_type,
    SUM(b.amount) AS Facturacion_Total,
    SUM(CASE WHEN b.payment_status = 'Paid' THEN b.amount ELSE 0 END) AS Monto_Pagado,
    SUM(CASE WHEN b.payment_status = 'Pending' THEN b.amount ELSE 0 END) AS Monto_Pendiente,
    SUM(CASE WHEN b.payment_status = 'Cancelled' THEN b.amount ELSE 0 END) AS Monto_Cancelado
FROM hospital_md.treatments AS t
JOIN hospital_md.billing AS b
    ON t.treatment_id = b.treatment_id
GROUP BY t.treatment_type
ORDER BY Facturacion_Total DESC;
```
<p align="center"><img src= img/pregunta12.png>

La facturación total se concentra principalmente en **Chemotherapy**, con **S/ 128,855.68**, seguida de **MRI** con **S/ 116,098.16** y **X-Ray** con **S/ 110,653.67**. En conjunto, estos tres tipos representan los mayores montos facturados del conjunto analizado.

Al comparar los estados de pago, **Chemotherapy presenta el mayor monto pendiente**, con **S/ 51,100.55**, superior a su monto pagado de **S/ 32,607.26**. En **MRI**, el monto pagado **(S/ 43,064.42)** supera al pendiente **(S/ 36,909.79)**, mientras que en **X-Ray** también predomina el monto pagado, con **S/ 47,978.78** frente a **S/ 34,839.29** pendientes. **Physiotherapy** registra **S/ 32,251.38 pagados** y **S/ 21,084.35 pendientes**, mientras que **ECG** presenta **S/ 17,523.06 pagados** y **S/ 40,678.03 pendientes.**

`Decisión sugerida`: El director podría priorizar la gestión de cobranza de Chemotherapy y ECG, debido a que en ambos tratamientos el monto pendiente supera al monto pagado, representando una mayor proporción de facturación aún no recaudada.

## Conclusiones

- **El uso de `GROUP BY`, `COUNT` y `ORDER BY` permitió segmentar la información de los pacientes y las citas para identificar diferencias entre categorías.** Por ejemplo, la distribución de pacientes mostró 31 registros masculinos y 19 femeninos, mientras que el análisis por estado de las citas permitió identificar 52 casos de No-show, 51 Scheduled, 51 Cancelled y 46 Completed.

- **El uso de funciones de agregación y condiciones con `CASE WHEN` permitió construir indicadores a partir de los datos originales.** En el análisis de las citas por motivo de visita, se calculó la cantidad y el porcentaje de citas no completadas, identificando que Emergency alcanzó el mayor porcentaje con 62,07%, seguido de Consultation con 60,47% y Therapy con 59,52%.

- **La combinación de `JOIN`, `GROUP BY` y funciones de agregación permitió relacionar información de diferentes tablas y obtener indicadores por entidad.** Esto se aplicó, por ejemplo, al relacionar `doctors` con `appointments` para calcular la carga de citas y el porcentaje de atenciones completadas por médico. El resultado permitió identificar que D005 concentró 29 citas, pero solo completó el 13,79% de ellas.

- **El uso de subconsultas y funciones de ventana permitió resolver consultas que requieren comparaciones dentro del conjunto de datos.** La subconsulta utilizada en la facturación permitió identificar pacientes cuya facturación acumulada superaba el promedio por paciente, mientras que `ROW_NUMBER() OVER (PARTITION BY...)` permitió obtener el tratamiento de mayor costo dentro de cada categoría. Esto demuestra la aplicación de SQL no solo para consultar registros, sino también para realizar análisis comparativos sobre los datos.

- **El uso de SQL permitió transformar datos dispersos en información útil para la toma de decisiones**, demostrando cómo las consultas, agrupaciones, filtros y relaciones entre tablas pueden utilizarse para detectar patrones y generar indicadores relevantes para la gestión hospitalaria.