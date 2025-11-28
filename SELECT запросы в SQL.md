tags: [sql, postgresql, database, bigdata, interviewprep]  
alias: [SELECT, SQL запросы, ВЫБРАТЬ]  
related: [[SQL]], [[Базы данных]], [[Реляционные базы данных]], [[JOINs]], [[Агрегатные функции в SQL]]


Оператор SELECT — это рабочая лошадка SQL. Он используется для извлечения (выборки) данных из одной или нескольких таблиц базы данных. Практически любая ваша работа с данными будет начинаться с него.

> **Важно понимать**: Порядок написания SQL-запроса не совпадает с логическим порядком его выполнения сервером.
> 
> - **Порядок написания**: SELECT, FROM, JOIN, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT.
>     
> - **Порядок выполнения**: FROM -> JOIN -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY -> LIMIT.
>     
> 
> Понимание этого порядка помогает понять, почему, например, нельзя использовать псевдоним (alias) из SELECT в условии WHERE.

Давайте разберем каждую часть запроса по порядку. Представим, что у нас есть таблица employees.

**Таблица employees:**  
| id | first_name | last_name | department | salary |  
|----|------------|-----------|------------|--------|  
| 1 | John | Doe | IT | 90000 |  
| 2 | Jane | Smith | HR | 75000 |  
| 3 | Peter | Jones | IT | 95000 |  
| 4 | Alice | Williams | Sales | 80000 |  
| 5 | Bob | Brown | Sales | 82000 |

### SELECT и FROM

- FROM указывает таблицу, из которой мы берем данные.
    
- SELECT указывает столбцы, которые мы хотим получить.
```SQL 
-- Выбрать все столбцы из таблицы employees
SELECT * FROM employees;

-- Выбрать только определённые столбцы
SELECT first_name, last_name, salary FROM employees;

-- Можно использовать псевдонимы (aliases) для столбцов
SELECT first_name AS name, salary AS monthly_salary FROM employees;
```

### WHERE

Фильтрует строки на основе заданного условия. Возвращает только те строки, для которых условие истинно.

```SQL
-- Найти всех сотрудников из отдела IT
SELECT * FROM employees WHERE department = 'IT';

-- Найти сотрудников, чья зарплата больше 80000
SELECT * FROM employees WHERE salary > 80000;

-- Можно использовать логические операторы AND, OR, NOT
SELECT * FROM employees WHERE department = 'Sales' AND salary > 80000;

-- Другие полезные операторы: IN, BETWEEN, LIKE
SELECT * FROM employees WHERE department IN ('IT', 'HR'); -- отдел IT или HR
SELECT * FROM employees WHERE last_name LIKE 'J%'; -- фамилия начинается на J
```

**WHERE работает до группировки.**

---

### ORDER BY

Сортирует результирующий набор данных по одному или нескольким столбцам.

- ASC (Ascending) — по возрастанию (по умолчанию).
    
- DESC (Descending) — по убыванию.

```SQL
-- Отсортировать сотрудников по зарплате по убыванию
SELECT first_name, salary FROM employees ORDER BY salary DESC;

-- Сортировка по нескольким столбцам: по отделу, а внутри отдела - по зарплате
SELECT first_name, department, salary FROM employees ORDER BY department ASC, salary DESC;
```

### LIMIT и OFFSET

Ограничивают количество возвращаемых строк.

- LIMIT — сколько строк вернуть.
    
- OFFSET — сколько первых строк пропустить.
    

Это очень полезно для **пагинации** (создания страниц с результатами).

```SQL
-- Найти 3 самых высокооплачиваемых сотрудника
SELECT first_name, salary FROM employees ORDER BY salary DESC LIMIT 3;

-- Найти 4-го и 5-го по зарплате сотрудника (пропустить 3, взять 2)
SELECT first_name, salary FROM employees ORDER BY salary DESC LIMIT 2 OFFSET 3;
```


### GROUP BY

Группирует строки с одинаковыми значениями в указанных столбцах в одну итоговую строку. Обычно используется с [[Агрегатные функции в SQL|агрегатными функциями]] (COUNT, SUM, AVG, MAX, MIN).

**Правило GROUP BY**: Любой столбец в SELECT, который не является агрегатной функцией, **должен быть** в GROUP BY.
```SQL
-- Посчитать количество сотрудников в каждом отделе
SELECT department, COUNT(*) AS number_of_employees
FROM employees
GROUP BY department;

-- Найти среднюю, максимальную и минимальную зарплату по отделам
SELECT
    department,
    AVG(salary) AS avg_salary,
    MAX(salary) AS max_salary,
    MIN(salary) AS min_salary
FROM employees
GROUP BY department;
```


### HAVING

Фильтрует результат **после** группировки. WHERE фильтрует строки до группировки, HAVING фильтрует сами группы.

```SQL
-- Найти отделы, в которых средняя зарплата больше 85000
SELECT
    department,
    AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 85000; -- Нельзя использовать WHERE avg_salary > 85000
```

### Популярные вопросы на собеседовании

- **"В чем разница между WHERE и HAVING?"**
    
    - Ответ: WHERE фильтрует строки до применения GROUP BY и агрегатных функций. HAVING фильтрует группы после того, как они были созданы GROUP BY и агрегатные функции были вычислены.
        
- **"Какой порядок выполнения операторов в SELECT запросе?"**
    
    - Ответ: FROM/JOIN -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY -> LIMIT.
        
- **"Напишите запрос, чтобы найти второго по величине... (например, зарплату)"**
    
    - Ответ: Часто решается через ORDER BY, LIMIT и OFFSET.