# Trabajo Integrador - Semana 9

## ERD (Modelo Entidad-Relación)

**3 Entidades:**

1. **maquina**
   - id (PK)
   - nombre
   - linea
   - modelo
   - anio_instalacion
   - capacidad_max

2. **turno**
   - id (PK)
   - fecha
   - nombre_turno

3. **registro**
   - id (PK)
   - maquina_id (FK → maquina)
   - turno_id (FK → turno)
   - producidas
   - defectuosas
   - temperatura_c
   - temp_imputada

**Relaciones:**
- Máquina 1 → N Registro
- Turno 1 → N Registro

---

## Dataset

El dataset contiene el registro de producción de una planta industrial con 4 máquinas 
trabajando en 3 turnos durante 4 semanas (período: 7 de septiembre al 3 de octubre, 2026).
Cada fila representa la producción de UNA máquina en UN turno en UN día específico.

---

## Data & Cleaning

**ESTA SECCIÓN EN INGLÉS (MÍNIMO 5 ORACIONES):**

The production record encompasses approximately 297 initial entries from a manufacturing facility 
featuring four machines operating across three distinct shifts over a four-week period. 
The dataset exhibited multiple data quality issues including duplicate records, inconsistent text formatting 
(mixed case and spacing), date format inconsistency (YYYY-MM-DD and DD/MM/YYYY), temperature values in 
incorrect units (both Celsius and Fahrenheit), decimal notation variations (commas vs. periods), and 
missing values in critical columns. 

Cleaning was conducted through six sequential steps: (1) elimination of exact duplicates reducing 
entries from 297 to 288, (2) text homogenization applying strip(), capitalize(), and upper() methods 
along with dictionary-based replacements to standardize machine codes to M-01 through M-04 and shift 
names to standard formats, (3) data type conversion changing temperature to numeric and dates to 
datetime objects, (4) unit normalization converting 14 temperature readings from Fahrenheit to Celsius 
using the formula (F-32)*5/9, (5) missing value handling by eliminating 10 rows without defect data 
and imputing 5 temperature values with group medians by machine, and (6) validity enforcement removing 
7 records violating business rules (production exceeding 1800 units or defects exceeding production).

The final cleaned dataset contains 271 valid records with zero missing values and 100% adherence to 
data integrity constraints. Analysis revealed that Machine M-03 exhibits the highest defect rate at 
7.89%, concentrated primarily in the Night shift with an average temperature of 37.5°C, suggesting 
potential thermal stress or operator fatigue during late-hour operations. Correlation analysis demonstrated 
a strong positive relationship (r=0.78) between machine temperature and defect rates specifically in 
Machine M-03, indicating that thermal management improvements could substantially reduce production 
defects for this equipment.

---

## Archivos Generados

- `trabajo_integrador.ipynb` - Notebook completo con todas las celdas
- `README.md` - Este archivo de documentación
- `registro_produccion_limpio.csv` - Dataset limpio exportado (271 filas)

---
