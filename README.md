<p align="center"><img src= img/portada.png>

# Proyecto SQL: Datos a decisiones - Análisis de Atenciones del Hospital Management Dataset

## Resumen (Overview)

El director general del hospital  Management Dataset desea obtener una visión clara de las principales operaciones de la institución. Sin embargo, necesita información consolidada que le permita conocer el comportamiento de los pacientes y las atenciones realizadas, así como identificar los principales motivos de visita y el estado de las citas.

Mi objetivo es utilizar SQL para analizar los datos de gestión hospitalaria y proporcionar información que facilite el análisis de las principales operaciones del hospital, considerando los datos de pacientes y citas registrados en la base de datos

## Estructura del proyecto

- [Base de datos](#base-de-datos)
- [Tareas](#tareas)
- [Limpieza y reparación de datos](#limpieza-y-reparación-de-datos)
- [Análisis exploratorio de Datos](#análisis-exploratorio-de-datos)

## Base de datos

Los datos originales se pueden encontrar [aquí](https://www.kaggle.com/datasets/kanakbaghel/hospital-management-dataset?select=appointments.csv).

La información se encuentra organizada en cinco tablas relacionadas entre sí: `patients`, `doctors`, `appointments`, `treatments` y `billing`.

La estructura de la base de datos permite seguir el flujo de atención desde el registro del paciente y la programación de una cita, hasta la asignación del médico, la realización de tratamientos y la facturación correspondiente, distribuidos en más de 5, 000 registros y 39 columnas.

## Tareas

En este análisis, ayudo al director del hospital a respoder las siguientes interrogantes:

1. ¿Cuántos pacientes están registrados en el hospital y cómo se distribuyen según género?
2. ¿Cuántos pacientes se registraron durante cada año y cuál fue el año con mayor cantidad de nuevos registros?
3. ¿Cómo se distribuyen las citas según su estado y cuál es la cantidad de citas completadas, canceladas y no asistidas?
4. ¿Cuáles son los principales motivos de visita y cuántas citas corresponden a cada uno?
5. ¿Cuántas citas ha atendido cada médico y cuál es su especialidad y sede hospitalaria?
6. ¿Qué especialidades médicas concentran la mayor cantidad de citas y cuál es el promedio de años de experiencia de sus médicos?
7. ¿Qué tipos de tratamiento se realizan con mayor frecuencia y cuál es el costo promedio de cada tipo de tratamiento?
8. ¿Qué tratamientos tienen un costo superior al costo promedio de todos los tratamientos registrados?
9. ¿Qué pacientes presentan un monto total facturado superior al promedio de facturación por paciente?
10. ¿Cuál es el tratamiento de mayor costo dentro de cada tipo de tratamiento?
11. ¿Cuál es el tratamiento de mayor costo de cada categoría y cómo se compara su costo con el costo promedio de su respectiva categoría?
12. ¿Qué tipos de tratamiento concentran los mayores montos de facturación y cómo se distribuyen estos montos según el estado de pago?

## Limpieza y reparación de datos

Antes de realizar el análisis, se llevó a cabo una revisión de las cinco tablas de la base de datos con el propósito de verificar la calidad y consistencia de la información. 

### Valores nulos

Primero, se verifica los valores nulos en las tablas.

```sql
-- Verificar valores faltantes en la tabla Appointments --

SELECT *
FROM appointments
WHERE appointment_id IS NULL;

--Verificar valores faltantes en la tabla Patients--

SELECT *
FROM patients
WHERE patient_id IS NULL;

--Verificar valores faltantes en la tabla Doctors--

SELECT *
FROM doctors
WHERE doctor_id IS NULL;

--Verificar valores faltantes en la tabla Billing--

SELECT *
FROM billing
WHERE bill_id IS NULL;

--Verificar valores faltantes en la tabla Treatments--

SELECT *
FROM treatments
WHERE treatment_id IS NULL;

```
**Resultado** : No se encontraron valores nulos en las tablas

### Registros duplicados

A continuación, se verificó la existencia de registros duplicados en los identificadores principales de las cinco tablas.

```sql
-- Verificar valores duplicados en la tabla Appointments --

SELECT appointment_id, COUNT(*)
FROM appointments
GROUP BY appointment_id
HAVING COUNT(*) > 1;

-- Verificar valores duplicados en la tabla Patients --

SELECT patient_id, COUNT(*)
FROM patients
GROUP BY patient_id
HAVING COUNT(*) > 1;

-- Verificar valores duplicados en la tabla Doctors --

SELECT doctor_id, COUNT(*)
FROM doctors
GROUP BY doctor_id
HAVING COUNT(*) > 1;

-- Verificar valores duplicados en la tabla Billing --

SELECT bill_id, COUNT(*)
FROM billing
GROUP BY bill_id
HAVING COUNT(*) > 1;

-- Verificar valores duplicados en la tabla Treatments --

SELECT treatment_id, COUNT(*)
FROM treatments
GROUP BY treatment_id
HAVING COUNT(*) > 1;
```

**Resultado**: No se identificaron registros duplicados en los campos clave, por lo que los registros mantienen identificadores únicos.

### 
