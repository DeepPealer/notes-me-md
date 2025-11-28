tags: [sql, postgresql, database, bigdata, interviewprep]  
alias: [JOIN, СОЕДИНЕНИЕ, INNER JOIN, LEFT JOIN, RIGHT JOIN, FULL OUTER JOIN, CROSS JOIN]  
related: [[SQL]], [[SELECT запросы в SQL]], [[Базы данных]], [[Подзапросы (Subqueries)]]

JOIN (объединение) — это операция, которая позволяет комбинировать строки из двух или более таблиц на основе связанного между ними столбца. Обычно связь устанавливается между **первичным ключом (primary key)** одной таблицы и **внешним ключом (foreign key)** другой.

Для примеров будем использовать две таблицы: employees и departments.

**Таблица employees:**  
| id | name | department_id |  
|----|-------|---------------|  
| 1 | John | 1 |  
| 2 | Jane | 2 |  
| 3 | Peter | 1 |  
| 4 | Alice | NULL |

**Таблица departments:**  
| id | name |  
|----|-----------|  
| 1 | IT |  
| 2 | HR |  
| 3 | Marketing |

Обратите внимание: у сотрудника Alice нет отдела, а у отдела Marketing нет сотрудников. Это поможет нам увидеть разницу между типами JOIN.

---

## [[INNER JOIN]] (Внутреннее соединение)

**Идея**: Возвращает только те строки, для которых есть совпадение в **обеих** таблицах. Это самый строгий и часто используемый тип соединения.

**Когда использовать?** Когда вам нужны данные, которые гарантированно связаны друг с другом. Например, "показать всех сотрудников, у которых точно есть отдел".

**Синтаксис и Пример:**

```SQL
SELECT employees.name, departments.name AS department_name
FROM employees
INNER JOIN departments ON employees.department_id = departments.id;
```

**Результат:**  
| name | department_name |  
|-------|-----------------|  
| John | IT |  
| Jane | HR |  
| Peter | IT |  
Alice не попала в результат, так как у нее department_id равен NULL. Отдел Marketing также не попал, так как на него не ссылается ни один сотрудник.


## [[LEFT JOIN]] (Левое внешнее соединение)

Также можно писать LEFT OUTER JOIN, слово OUTER является необязательным.

**Идея**: Возвращает **все** строки из **левой** таблицы (employees) и только совпадающие строки из правой таблицы (departments). Если совпадения в правой таблице нет, соответствующие столбцы будут заполнены NULL.

**Когда использовать?** Когда вам нужны все данные из одной таблицы, даже если для них нет пары в другой. Например, "показать всех сотрудников и их отделы, даже если отдел не назначен".

**Синтаксис и Пример:**
```SQL
SELECT employees.name, departments.name AS department_name
FROM employees
LEFT JOIN departments ON employees.department_id = departments.id;
```
**Результат:**  
| name | department_name |  
|-------|-----------------|  
| John | IT |  
| Jane | HR |  
| Peter | IT |  
| Alice | NULL |  
Alice включена в результат, но ее department_name равен NULL.

---

## [[RIGHT JOIN]] (Правое внешнее соединение)

Также RIGHT OUTER JOIN.

**Идея**: Зеркальное отражение LEFT JOIN. Возвращает **все** строки из **правой** таблицы (departments) и совпадающие из левой (employees).

**Когда использовать?** Когда вам нужны все данные из второй таблицы. Например, "показать все отделы и работающих в них сотрудников, даже если в отделе никто не работает". На практике используется реже, так как любой RIGHT JOIN можно переписать в более привычный LEFT JOIN, поменяв таблицы местами.

**Синтаксис и Пример:**
```SQL
SELECT employees.name, departments.name AS department_name
FROM employees
RIGHT JOIN departments ON employees.department_id = departments.id;
```

**Результат:**  
| name | department_name |  
|-------|-----------------|  
| John | IT |  
| Peter | IT |  
| Jane | HR |  
| NULL| Marketing |  
Отдел Marketing включен в результат, но имя сотрудника для него равно NULL.

---

## [[FULL OUTER JOIN]] (Полное внешнее соединение)

**Идея**: Комбинация LEFT и RIGHT JOIN. Возвращает все строки из обеих таблиц. Если для строки из одной таблицы нет совпадения в другой, недостающие поля заполняются NULL.

**Когда использовать?** Когда нужно увидеть полную картину данных из обеих таблиц, включая все несовпадения. Например, "показать всех сотрудников и все отделы, сопоставив их там, где это возможно".

**Синтаксис и Пример:**
```SQL
SELECT employees.name, departments.name AS department_name
FROM employees
FULL OUTER JOIN departments ON employees.department_id = departments.id;
```

**Результат:**  
| name | department_name |  
|-------|-----------------|  
| John | IT |  
| Jane | HR |  
| Peter | IT |  
| Alice | NULL |  
| NULL| Marketing |  
В результат попали и Alice, и отдел Marketing.

---

## [[CROSS JOIN]] (Перекрестное соединение)

**Идея**: Возвращает декартово произведение двух таблиц. Каждая строка из первой таблицы соединяется с каждой строкой из второй. Условие ON не используется.

**Когда использовать?** Редко. В основном для генерации тестовых данных или получения всех возможных комбинаций (например, все размеры для всех цветов товара). Будьте осторожны: результат может быть огромным!

**Синтаксис и Пример:**
```SQL
SELECT employees.name, departments.name AS department_name
FROM employees
CROSS JOIN departments;
```

**Результат:** (12 строк = 4 сотрудника * 3 отдела)  
| name | department_name |  
|-------|-----------------|  
| John | IT |  
| John | HR |  
| John | Marketing |  
| Jane | IT |  
| ... | ... |

---

### Популярные вопросы на собеседовании

- **"В чем разница между INNER JOIN и LEFT JOIN?"**
    
    - Ответ: INNER JOIN возвращает только строки, у которых есть совпадения в обеих таблицах. LEFT JOIN возвращает все строки из левой таблицы, даже если для них нет совпадений в правой (в этом случае поля правой таблицы будут NULL).
        
- **"Как найти всех сотрудников, у которых нет отдела?"**
    
    - Ответ: Это классический вопрос для LEFT JOIN.
```SQL
SELECT e.name
FROM employees e
LEFT JOIN departments d ON e.department_id = d.id
WHERE d.id IS NULL; -- Ключевое условие
```

- **"Что такое CROSS JOIN и когда его можно использовать?"**