---
marp: true
title: Basic SQL
theme: gaia
class:
  - invert
---

# SELECT Setninger

- Henter data fra databasen 
- Hente alle kolonner med `*`
- Angi spesifikke kolonner
```sql
SELECT first_name
     , last_name
     , ... 
FROM employees;
```
```sql
SELECT * FROM employees;
```

---

# Kommentarer 
```sql
/* Alt mellom dette er en kommentar */

-- Det som kommer er en kommentar
```

---

# Kolonne Alias

```sql
SELECT first_name AS fornavn
     , last_name AS etternavn
FROM employees;
```
- Bruk `AS` for å gi en kolonne et nytt navn.
- Bruk __snakecase__ eller `"kolonne navn"` for å angi nytt navn.

---

# WHERE 

```sql
SELECT first_name AS fornavn
     , last_name AS etternavn
FROM employees
WHERE department_id = 10;
```

- `WHERE` clause filters rows based on a condition.

---

#  Operatorer

- `=` : lik
- `!=` : ulik
- `>` : Større enn
- `<` : Mindre enn
- `>=` : Større eller lik
- `<=` : Mindre eller lik

---

# Logiske Operatorer

- `AND` : Kombinerer flere kriterier, alle må være **TRUE**.
- `OR` : Kombinerer flere kriterier, en må være **TRUE**.
- `NOT` : Negativ av et kriterie.

```sql
SELECT first_name AS fornavn
     , last_name AS etternavn
FROM employees
WHERE department_id = 10 AND salary > 5000;
```

---

# IN Operator

- `kolonne IN (verdi_1, ...)` : brukes for å angi et sett av lovelig verdier 

```sql
SELECT department_id
FROM departments
WHERE department_name IN ('Sales','Human Resources');
```
---
# LIKE Operator

- `kolonne LIKE '%tekst%'` : brukes for å søke i to tekster.  

```sql
SELECT first_name AS fornavn
     , last_name AS etternavn
FROM employees
WHERE last_name LIKE '%sen";
```
---

# BETWEEN Operator

- `kolonne BETWEEN n AND m` : alt mellom `n` og `m`.
- tilsvarer `n <= kolonne AND kolonne >= m`

```sql
SELECT employee_id
     , first_name AS fornavn
     , last_name AS etternavn
FROM employees
WHERE employee_id BETWEEN 1 and 6 
```

--- 

# ORDER BY

```sql
SELECT first_name AS fornavn
     , last_name AS etternavn
FROM employees
ORDER BY last_name ASC;
```

- `ORDER BY` : setter rekkefølge på sortering.
- `ASC` : stigende rekkefølge (default).
- `DESC` : sykende rekkefølge.

---

# Aggregat Funksjoner

- `COUNT(kolonne)` : Antall rader.
- `SUM(kolonne)` : Sum.
- `AVG(kolonne)` : Gjennomsnitt.
- `MAX(kolonne)` : max verdi.
- `MIN(kolonne)` : min verdi.
    - **Merk**, `SUM` med `NULL` blir `NULL`, etc. 
```sql
SELECT COUNT(*) AS "totalt antall ansatte"
FROM employees;
```

---

# GROUP BY

```sql
SELECT department_id
     , COUNT(*) AS "totalt antall ansatte"
FROM employees
GROUP BY department_id;
```

- `GROUP BY` grupperer rader som deler sammen egenskap for bruk av aggregat funskjoner

---

# HAVING

```sql
SELECT employees_id, COUNT(*)
FROM employees
GROUP BY employees_id
HAVING COUNT(*) > 1;
```

- `HAVING` filterer gruppen etter aggregatet.
- Brukes ofte for å identifisere duplikater.

---

# JOINS

`INNER JOIN`,`LEFT JOIN`,`RIGHT JOIN`,`FULL OUTER JOIN`

![mengder](https://i0.wp.com/flowingdata.com/wp-content/uploads/2011/10/Keytar-platypus.png?w=717&quality=80&ssl=1)

---

# INNER JOIN

```sql
SELECT employees.first_name AS fornavn
     , employees.last_name AS etternavn
     , departments.department_name AS avdelingsnavn
FROM employees
INNER JOIN departments ON employees.department_id = departments.department_id;
```

- Henter rader med verdier i begge tabellene.
- Filterer bort rader fra begge tabellene, om nødvendig.

---

# LEFT JOIN

```sql
SELECT employees.first_name AS fornavn
     , employees.last_name AS etternavn
     , departments.department_name AS avdelingsnavn
FROM employees
LEFT JOIN departments ON employees.department_id = departments.department_id;
```

- Henter alle rader fra __employees__ tablen og de rader fra __departments__ som vi får treff på. 
- Filterer ikke vekk rader fra __employees__.

---

# RIGHT JOIN

```sql
SELECT employees.first_name AS fornavn
     , employees.last_name AS etternavn
     , departments.department_name AS avdelingsnavn
FROM employees
RIGHT JOIN departments ON employees.department_id = departments.department_id;
```

- Henter alle rader fra __departments__ tablen og de rader fra __employees__ som vi får treff på. 
- Filterer vekk rader fra __employees__.
---

# FULL OUTER JOIN

```sql
SELECT employees.first_name AS fornavn
     , employees.last_name AS etternavn
     , departments.department_name AS avdelingsnavn
FROM employees
FULL OUTER JOIN departments ON employees.department_id = departments.department_id;
```

- Henter alle rader fra både __departments__ og __employees__. 
- Filterer ikke noe vekk. 
- __employees__ kolonner som ikke treffer __departments__ kolonner vil ha `NULL` verdier is disse, og visa versa. 

---

# Tabel Alias

```sql
SELECT e.first_name AS fornavn
     , e.last_name AS etternavn
     , d.department_name AS avdelingsnavn
FROM employees e 
FULL JOIN departments d ON e.department_id = d.department_id;
```

- Henter alle rader fra både __departments__ og __employees__. 
- Filterer ikke noe vekk. 
- __employees__ kolonner som ikke treffer __departments__ kolonner vil ha `NULL` verdier is disse, og visa versa. 

---

# Subqueries

```sql
SELECT first_name AS fornavn
    , last_name AS etternavn
FROM employees
WHERE department_id = (SELECT department_id
                       FROM departments
                       WHERE department_name = 'Sales');
```

- Spørringer kan kjedes i sammen.
- Pass på granularitet 

---

# CTEer
```sql
/* 
  Bra for organisering.
  Deler opp hver kilde for å kunne enklere feilsøke.
*/ 
WITH 

departments_cte AS (
    SELECT *
    FROM departments
    WHERE department_name = 'Sales'
), 

employees_cte AS (
    SELECT * 
    FROM employees
),

SELECT first_name AS fornavn
    , last_name AS etternavn
FROM employees_cte
WHERE department_id = (
    SELECT department_id
    FROM departments_cte
)
;
```

---
# EXISTS Operator

```sql
SELECT first_name AS fornavn
    , last_name AS etternavn
FROM employees
WHERE EXISTS (
    SELECT 1 FROM departments
    WHERE employees.department_id = departments.department_id
)
;
```

- Sjekker om __employees__ sin __department_id__ eksisterer i __departments__. 

---

# CASE Setninger

```sql
SELECT first_name AS fornavn
    , last_name AS etternavn
    , CASE 
        WHEN salary > 10000 THEN 'Høy'
        WHEN salary BETWEEN 5000 AND 10000 THEN 'Middels'
        ELSE 'Lav'
      END AS lonnsklasse
FROM employees;
```

- Gir forholdsbestemt logikk. 

---

# Quiz 

---
# Dato Funskjoner
- `CURRENT_DATE` : dagens dato i følge databasen
- `TO_DATE` :  tekst til dato, krever format
- `EXCTRACT` : henter ut `YEAR`, `MONTH`, `DAY`
- `DATEADD` : legger til dato
```sql
SELECT * FROM employees WHERE start_date >= DATEADD/('MONTH',-6, CURRENT_DATE());
```
```sql
SELECT * FROM employees WHERE start_date >= TO_DATE('20230101','yyyymmdd');
```
```sql 
SELECT EXTRACT(YEAR FROM start_date) AS ansattelese_ar FROM employees 
```
---
# NULL Håndtering

- `NVL(kolonne, null_verdi)` 
- `COALESCE(kolonne_1, kolonne_2,  null_verdi)`
- `kolonne IS NULL`
- `kolonne IS NOT NULL`
---
# Tekst Håndtering
- `'Dette er en tekst'`
- `LOWER(tekst_kolonne)` 
- `UPPER(tekst_kolonne)`
- `CONCAT(tekst_kolonne_1,tekst_kolonne_2,...)` alternativt `tekst_kolonne_1||tekst_kolonne_2` 
---
# SUBSTR 
- `SUBSTR(tekst_kolonne,n,m)` : returnerer karakterene mellom bokstav `n` og `m` 
```sql
SELECT SUBSTR('ABCDEFG',3,4) "Substring"
     FROM DUAL;
-- Substring = CDEF
SELECT SUBSTR('ABCDEFG',-5,4) "Substring"
     FROM DUAL;
-- Substring = CDEF
```
---
# UNION 
```sql
SELECT col_1_a, col_2_a FROM tab_a 
UNION ALL 
SELECT col_1_b, col_2_b FROM tab_b 
```
- Alle rader fra `tab_b` og `tab_a`
- `col_1_a, col_2_a` må ha samme datatype som tisvarende i `tab_b`
- `UNION` uten `ALL`fjerner duplikater mellom `tab_a` og `tab_b`
---
# MINUS
```sql
SELECT col_1_a, col_2_a FROM tab_a 
MINUS 
SELECT col_1_b, col_2_b FROM tab_b 
```
- alle rader fra `tab_a` som ikke ligger i `tab_b`
- `col_1_a, col_2_a` må ha samme datatype som tisvarende i `tab_b`