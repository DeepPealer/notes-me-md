tags: [python, bigdata, datastructures, algorithms, interviewprep]  
alias: [Python Стек, Python Очередь]  
related: [[Algorithms and Data Structures]], [[Python list (Динамический массив)]], [[collections.deque]]

В Python нет отдельных встроенных классов Stack или Queue. Вместо этого мы используем другие, более универсальные структуры данных для их эмуляции. Выбор правильной структуры — ключ к эффективности.

---

## [[Стек (Stack)]] в Python

**Принцип: LIFO (Last-In, First-Out)**

Стек в Python проще всего и правильнее всего реализовывать с помощью обычного **list**. Методы list идеально ложатся на операции стека, и, что важно, они эффективны.

|   |   |   |
|---|---|---|
|Операция стека|Реализация на list в Python|Сложность|
|**Push**|my_list.append(item)|[[O(1)]]*|
|**Pop**|my_list.pop()|[[O(1)]]|
|**Peek**|my_list[-1]|[[O(1)]]|
|**isEmpty**|if not my_list:|[[O(1)]]|

*Амортизированная сложность. append работает за константное время в среднем.

**Пример кода:**
``` Python
# Инициализация стека
call_stack = []

# Push: добавляем элементы
call_stack.append("main()")
call_stack.append("function_A()")
call_stack.append("function_B()")

print(f"Стек вызовов: {call_stack}")  # -> ['main()', 'function_A()', 'function_B()']

# Peek: смотрим на вершину
print(f"На вершине стека: {call_stack[-1]}") # -> 'function_B()'

# Pop: удаляем элемент с вершины
last_func = call_stack.pop()
print(f"Завершилась функция: {last_func}") # -> 'function_B()'
print(f"Стек после pop: {call_stack}") # -> ['main()', 'function_A()']
```

> **Вывод для собеседования**: Для реализации стека в Python используйте list. Это просто, идиоматично и эффективно.

---

## [[Очередь (Queue)]] в Python

**Принцип: FIFO (First-In, First-Out)**

Здесь ситуация сложнее и это **очень частый вопрос-ловушка на собеседованиях**.

#### **Неправильный подход**: Использование list

Можно попытаться реализовать очередь на list:

- enqueue -> my_list.append(item) (быстро, O(1))
    
- dequeue -> my_list.pop(0) (**ОЧЕНЬ МЕДЛЕННО**, O(n))
    

Операция pop(0) требует сдвига всех оставшихся элементов списка на одну позицию влево. На больших списках это приводит к катастрофической потере производительности.

#### **Правильный подход**: Использование collections.deque

Для эффективной реализации очереди в Python нужно использовать collections.deque (произносится "дек", сокращение от double-ended queue, двусторонняя очередь).

deque построен на основе **двусвязного списка**, что обеспечивает быстрое (O(1)) добавление и удаление элементов с обоих концов.

|   |   |   |
|---|---|---|
|Операция очереди|Реализация на deque в Python|Сложность|
|**Enqueue**|my_deque.append(item)|[[O(1)]]|
|**Dequeue**|my_deque.popleft()|[[O(1)]]|
|**Peek**|my_deque[0]|[[O(1)]]|
|**isEmpty**|if not my_deque:|[[O(1)]]|

**Пример кода:**

``` Python
from collections import deque

# Инициализация очереди
printer_queue = deque()

# Enqueue: запросы на печать ставятся в очередь
printer_queue.append("document1.pdf")
printer_queue.append("photo.jpg")
printer_queue.append("document2.pdf")

print(f"Очередь на печать: {printer_queue}") # -> deque(['document1.pdf', 'photo.jpg', 'document2.pdf'])

# Peek: смотрим, кто первый на очереди
print(f"Следующий на печать: {printer_queue[0]}") # -> 'document1.pdf'

# Dequeue: принтер забирает задание
current_job = printer_queue.popleft()
print(f"Печатается: {current_job}") # -> 'document1.pdf'
print(f"Очередь после dequeue: {printer_queue}") # -> deque(['photo.jpg', 'document2.pdf'])
```
### Сводная таблица и ключевая мысль

|   |   |   |
|---|---|---|
|Задача|НЕПРАВИЛЬНО / МЕДЛЕННО|ПРАВИЛЬНО / БЫСТРО|
|**Реализовать Стек**|-|list (append, pop)|
|**Реализовать Очередь**|list (append, pop(0))|collections.deque (append, popleft)|

**Вопрос:** "Почему не стоит использовать list для очереди?"  
**Ответ:** "Потому что удаление элемента из начала списка (pop(0) или del list[0]) имеет временную сложность O(n), так как все последующие элементы должны быть сдвинуты. Это делает очередь неэффективной. collections.deque решает эту проблему, обеспечивая сложность O(1) для удаления слева (popleft()), так как он основан на двусвязном списке."

---