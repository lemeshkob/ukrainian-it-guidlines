[<-- Повернутись назад](index.md)

## Зрозумійте різні концепції ОС

Перш ніж ознайомлюватися детально про концепції операційних систем, необхідно мати загальне уявлення про те, що таке саме операційна система та які її задача, принципи. Ознайомитися з цим можна у статті [Операційна система (ОС)](../global/operating-system.md)

> Ця стаття буде досить великих розмірів, так як необхідної, важливої інформації немало. Інформація буде подана таким самим способом, як й у оригіналу, тобто тезами. Якщо щось не до кінця зрозуміло, то це не смертельно. Тема досить обширна та складна, щоб її повністю опанувати, потрібен досвід. Для ознайомлення та суто загального поняття досить й інформації зі статті, посилання на яку вище.
> 
> Для наглядних прикладів прикладів буде братися ОС Лінукс.

### Процес і управління процесами

**Процес**: примірник програми, що виконується.

- Стан виконання: лічильник програм, покажчик стека
- Частини та зона тимчасового утримання: дані, стан регістра, займає стан в пам'яті
- Може знадобитися спеціальне обладнання: пристрої введення-виведення
    
Процес - це стан програми під час виконання та завантаження в пам'ять, тобто активний стан виконання.

#### Як виглядає процес?

![Process](images/process.png)

#### Тип стану

- Текст та данні: статичний стан, коли процес завантажується першим
- Купа / Heap: динамічно створюється під час виконання
- Стек / Stack: росте і зменшується; черга LIFO
    
#### Як ОС знає, що виконує процес?

Використовується:

* Лічильник програм
* Регістри процесора
* Вказівник стека

#### Блок керування процесом (PCB)

![PCB](images/pcb.png)

* PCB створюється, коли створюється процес
* Деякі поля оновлюються при зміні стану процесу, наприклад. відображення пам'яті
* або інші поля, які дуже часто змінюються, наприклад, ліічильник програм

#### Як використовується PCB?

![howpcbisused](images/howpcbisused.png)

#### Перемикач контексту

- Механізм, що використовується для перемикання від контексту одного процесу до іншого в ЦП.

- Вони затратні!
	- прямі затрати: кількість циклів для інструкцій завантаження та зберігання.
	- непрямі затрати: холодний кеш (аналогія з непрогрітим двигуном). Коли кеш горячий, більшість даних процесу знаходиться в кеші, тому продуктивність процесу буде найкращою.


Іноді бувають ситуації, коли нам доводиться перемикати контекст (процес з високим пріоритетом, розподіл часу тощо)

#### Життєвий цикл процесу

![processlifecycle](images/processlifecycle.png)

ЦП може виконувати процес, коли процес знаходиться в стані Running або Ready.

#### Створення процесу

Механізми:

- вилка / fork:
	- копіює "батьківську" PCB в нову дочірню PCB
	- дитина містить виконання за вказівкою після fork
    
- виконавчий / exec:
	- замінити дочірнє зображення
	- завантажити нову програму та почати з першої інструкції
   
#### Яка роль планувальника ЦП?

Планувальник ЦП визначає, який із готових процесів буде надіслано до ЦП, щоб почати працювати, і як довго він повинен працювати.

ОС повинна:

* випереджувати / preempt - переривати і зберігати поточний контекст
* планувати / schedule - запустити планувальник, щоб вибрати наступний процес
* диспетчеризувати / dispatch - відправити процес 2 перемикатись на його контекст

#### Планування проектувальних рішень

![timeslice](images/timeslice.png)

* Які значення мають відповідні тимчасові фрагменти?
* Показники для вибору наступного процесу для запуску?

#### Введення-Виведення / I/O

Процес може звільнитися в черзі готовності кількома способами.

![io](images/io.png)

#### Чи може процес взаємодіяти?

##### Міжпроцесний зв'язок:

Механізми IPC:

- передача даних/інформації між адресним простором 
- підтримання захисту та ізоляції 
- забезпечення гнучкості і продуктивністі

Два типи моделей IPC:

1. Передача повідомлення IPC
![messagepassing](images/messagepassing.png)

* ОС забезпечує загальний буфер лінії зв'язку
* Процеси можуть записувати (відправляти) / write(send), читати (отримувати) / read(receive) повідомлення в/з каналу 
Переваги: ОС керує каналом
Недоліки: накладні витрати
    
2. Спільна пам'ять IPC

![sharedmemory](images/sharedmemory.png)

* ОС встановлює спільний канал і відображає його в адресному просторі кожного процесу 
* Обробляє безпосередньо запис (відправлення), читання (отримання) повідомлення в/з цієї пам'яті

Переваги: ОС не працює після встановлення спільного каналу
Недоліки: Повторна реалізація великої кількості коду, який могла б зробити ОС

Загалом, зв’язок на основі спільної пам’яті краще, якщо відображення пам’яті між двома процесами амортизується на велику кількість повідомлень.

### Потоки та паралельність

##### Потоки:

- активні: одиничне виконання процесу 
- працють одночасно з іншими: багато потоків виконуються разом 
- вимагають координації: спільне використання пристроїв вводу/виводу, процесорів, пам'яті
    
#### Прочесс vs Потік /  Process vs Thread

![processvthread](images/processvthread.png)

#### Чим потоки корисні?

* Паралелізація => Прискорення / Parallelization => Speedup
* Спеціалізація => Гарячий кеш / Specialization => Hot cache
* Ефективність => менша потреба в пам'яті та дешевший IPCE
* Час для перемикання контексту в потоках менше, оскільки пам'ять є спільною. Отже, не потрібно зіставлення між віртуальною та фізичною пам'яттю, тому багатопотоковість можна використовувати, щоб приховати затримку.
* Переваги як для програм, так і для коду ОС
	- Багатопотокове ядро ОС
		- потоки, що працюють від імені програм
		- Служби рівня ОС, як драйвери

#### Що необхідно для підтримки потоків?

* Структура даних потоків
	- Визначайте потоки, відстежуйте використання ресурсів...
* Механізми створення потоків і керування ними
* Механізми безпечної координації між потоками, що працюють одночасно в одному адресному просторі

#### Контроль і координація паралельності

- Взаємовиключення
	- Ексклюзивний доступ лише до одного потоку за раз
    - мьютекс / mutex
- Очікування на інші потоки
	- Конкретні умови перед продовженням
    - умовна змінна / condition variable
- Виведення інших потоків із стану очікування

#### Потоки та створення потоків

- Структура даних потоку:
	- Тип потоку / Thread type, ідентифікатор потоку / Thread ID, PC, SP, регістри / registers, стек / stack, атрибути / attributes

- Fork (proc, args)
	- cстворити потік
	- не UNIX fork
    
```
t1 = fork(proc, args)   
```

- Join(потік)
	- завершити потік

```
child_result = join(t1)   
```

##### Приклад:

```
Thread  t1;
Shared_List list;
t1 = fork(safe_insert, 4);
safe_insert(6);
join(t1); //Optional
```

Доступ до списку можна отримати, прочитавши спільну змінну.

#### Взаємовиключення

- Mutex структура даних:
	- locked?, owner, blocked_threads
    
```
lock(mutex){
	//Критичний розділ / Critical Section
    //Одночасно може отримати доступ лише один потік / Only one thread can access at a time
}
unlock(mutex)
```
![mutex](images/mutex.png)

#### Задача постачальника-споживача / Producer Consumer problem

Що робити, якщо обробка, яку ви бажаєте виконати із взаємним виключенням, має відбуватися за певних умов? Наприклад. Постачальник додає елементи до списку, поки список не буде заповнений, а споживач повинен роздрукувати всі позиції списку, якщо список заповнений, а потім очистити список. Таким чином, ми повинні виконати потік Consumer лише за певної умови (тут – коли список стане заповненим, друкувати елементи).

Задача постачальника-споживача - це класичний приклад задачі синхронізації кількох процесів. Задача описує два процеси, постачальник і споживач, які спільно використовують буфер встановленого розміру. Завданням постачальника є створення фрагменту даних, запис його до буфера і повторення цих дій раз за разом. Одночасно з цим споживач споживає дані (тобто, видаляє їх з буфера) по одному фрагменту за раз. Задача полягає в тому, щоб не дати постачальнику записати дані, коли буфер повний, а споживачу не дати видалити дані з порожнього буфера. 

Рішення: використати **Змінні умови**

- Wait(mutex, condition)
	- mutex автоматично звільняється і вимагається під час очікування
	- Споживач застосовує "Очікування", поки список не заповниться
    
- Signal(condition)    
	- Повідомити лише один потік, що очікує за умови
    - The Producer applies _Signal_ to the Consumer thread when the list is full
- Broadcast(condition)    
	- Notify all waiting threads
    
![producerconsumer](images/producerconsumer.png)    

## Readers / Writer problem

* 0 or more readers can access a resource
* 0 or 1 writer can write the resource concurrently at the same time

- One solution:
	- lock on resource
    	- good for writer
    	- too restrictive for readers
        
- Better solution:

```
if ((read_count == 0) & (read_count == 0))
	R okay, W okay
if (read_count > 0)
	R okay    
if (read_count == 1)
	R not-okay, W not-okay    
```

State of shared resource:

* free : resource_counter = 0
* reading : resource_counter > 0
* writing : resource_counter = -1

Thus essentially we can apply mutex on the new proxy 'resource_counter' variable that represents the state of the shared resource.

## Avoiding common mistakes

- keep track of mutex/lock variable used with a resource
	- e.g. mutex_type m1; // mutex for file1
- check that you are always and correctly using lock and unlock 	- Compilers can be used as they generate errors/warnings to correct this type of mistake  	
- Use a single mutex to access a single resource
- check that you are signalling correct condition
- check that you are not using signal when broadcast is needed
	- signal : only 1 thread is will proceed, remaining threads will wait
- check thread execution order to be controlled by signals to condition variables

## Spurious(Unnecessary) Wake ups

When we wake up threads knowing they may not be able to proceed.

## Deadlocks

Two or more competing threads are said to be in a deadlock if they are waiting on each other to complete, but none of them ever do.

![deadlock](images/deadlock.png)

Here T1 and T2 are in deadlock.

### How to avoid this?

1. Unlock T1 before locking T2
	- Fine-grained locking but T1 nad T2 may both be required
2. Use one mega lock, get all locks upfront, then release at end
	- For some applications this may be ok. But generally its too restrictive and limits parallelism
3. Maintain lock order
	- first m_T1
    - then m_T2 
    	- this will prevent cycles in wait graph

A cycle in wait graph is necessary and sufficient for deadlock to occur.<br> 
(thread-waiting-on-resource ---edge---> thread-owning-resource)

* Deadlock prevention => Expensive<br>
Pre-check for cycles and then delay process or change code

* Deadlock Detection and Recovery => Rollback

## Kernel vs User level Threads

![kernelvuserthread](images/kernelvuserthread.png)

Three types of models:

#### 1. **One to One model**:

![onetoone](images/onetoone.png)

**Advantages**: 

* OS sees threads
* Synchronization
* Blocking

**Disadvantages**: 

* Must go to OS for all operations
* OS may have limits on policies, threads
* Portability

#### 2. **Many to One model**:

![manytoone](images/manytoone.png)

**Advantages**: 

* Totally Portable 
* Doesn't depend on OS limits and policies

**Disadvantages**: 

* OS may block entire process if one user-level thread blocks on I/O

#### 3. **Many to Many model**:

![manytomany](images/manytomany.png)

**Advantages**: 

* Best of both worlds
* Can have bound or unbound threads

**Disadvantages**: 

* Requires coordination between user and kernel level thread managers

## Multithreading patterns

**1. Boss-Workers pattern**

* Boss- assigns work
* Workers- perform entire task

Throughput of system is limited by boss thread. Hence boss thread must be kept efficient.

Throughput = 1/boss-time-orders

Boss assigns works by:
1. Directly signalling specific works
	- **\+** workers don't need to sync
    - **\-** boss must keep track of everyone
2. Placing work in queue
	- **\+** boss doesn't neeed to know details about workers
    - **\-** queue synchronization

How many workers?
- on demand
- pool of workers
- static vs dynamic (i.e dynamically increasing size according to work)

**Advantages**: 

* Simplicity

**Disadvantages**: 

* Thread pool management
* Locality

**1B. Boss-Workers pattern variant**

* Here workers are specialized for certain tasks opposite to the previous equally created workers

**Advantages**: 
* Better locality
* Quality of Service management

**Disadvantages**: 
* Load balancing

**2. Pipeline pattern**

* Threads assigned one subtask in the system
* Entire task = Pipeline of threads
* Multiple tasks concurrently run in the system, in different pipeline stages
* Throughput depends on weakest link
* Shared buffer based communication between stages

**3. Layered pattern**

* Layers of threads are assigned group of related subtasks
* End to end task must pass up and down through all layers

**Advantages**: 
* Specialization
* Less fine-grained than pipeline

**Disadvantages**: 
* Not suitable for all applications
* Synchronization

### Example:

**Q)** For 6 step toy order application we have 2 solutions:

1. Boss-workers solution
2. Pipeline solution

Both have 6 threads. In the boss-workers solution, a worker produces a toy order in 120 ms. In the pipeline solution, each of 6 stages take 20 ms.

How long will it take for these solutions to complete 10 toy orders and 11 toy orders?

**A)** 6 threads means for Boss-workers, 1 thread is for boss, 5 for workers. In pipeline 6 threads are equally used.

For 10 toy orders:
```
Boss-workers(10) = 120 + 120 = 240 ms
Pipeline(10) = 120 + (9*20) = 300 ms
```
Here Boss-workers is better than Pipeline.

For 11 toy orders:
```
Boss-workers(11) = 120 + 120 + 120 = 360 ms
Pipeline(11) = 120 + (10*20) = 320 ms
```
Here Pipeline is better than Boss-workers.

This proves that choosing a better pattern depends on the number of threads and the work required to be done.

## PThreads

PThreads == POSIX Threads

POSIX = Portable OS interface

### Compiling PThreads

1. #include<pthread.h> in main file
2. Compile source with -lpthread or -pthread
```
gcc -o main main.c -lpthread
gcc -o main main.c -pthread
```
3. Check return values of common examples


### PThread mutexes
- to solve mutual exclusion problems among concurrent threads

### Safety tips

* Shared data should always be accessed through single mutex
* Mutex scope must be visible to all
* Globally order locks
	- for all threads, lock mutexes in order
* Always unlock a mutex (correctly)

## Thread Design Considerations

### Kernel vs User Level Threads

![userlevelvkernellevel](images/userlevelvkernellevel.png)

### Thread related data structures

![threadds](images/threadds.png)

### Hard vs Light Process states

PCB is divided into multiple data structures classified as follows:

- Light Process states
	- Signal mask 
    - System call args
- Heavy Process states 
	- virtual address mapping
    
#### Rationale for Multiple Data Structures:

|Single PCB |Multiple DS|
|-----------|-----------|
|Large continuos DS|Smaller DS|
|Private for each entity|Easier to share|
|Saved and restored on each context switch|Save and Restore only what needs to change on context switch|
|Update for any changes|User lever library need to only update portion of the state|

- Thus the following disadvantages for single PCB become advantages for Multiple DS : 
  * Scalability
  * Overheads
  * Performance
  * Flexibility
  
## Comparison of Interrupts and Signals

- Handled in specific ways
        - interrupt and signal handlers
    - Can be ignored
        - interrupt and signal mask
    - Expected or unexpected    
        - appear synchronously or asynchronously        

* Difference:

|Interrupts |Signals|
|-----------|-----------|
|Events generated externally by components other than CPU (I/O devices, timers, other CPUs)|Events triggered by CPU and software running on it|
|Determined based on physical platform|Determined based on OS|
|Appear asynchronously|Appear synchronously or asynchronously|

- Similarities:
	- Have a unique ID depending on h/w or OS
    - Can be masked and disabled/suspended via corresponding mask
    	- per-CPU interrupt mask, preprocess signal mask
	- if enabled, trigger corresponding to handler   
    	- interrupt handler set for entire system by OS
        - signal handler set on per process basis by process
        
> An interrupt is like a snowstorm alarm<br>
> A signal is like a low battery warning

### Interrupts

![interrupts](images/interrupts.png)

### Signals

![signals](images/signals.png)

#### Handlers / Actions
- Default actions
	- Terminate, ignore
    - Terminate and core dump
    - Stop or continue
- Process Installs Handler
	- signal(), sigaction()
    - for most signals, some cannot be "caught"
- **Synchronous**
	- SIGSEGV (access to protected memory)
    - SIGFPE (divided by zero)
    - SIGKILL (kill, id)
    	- can be directed to a specific thread
- **Asynchronous***        
	- SIGKILL (kill)
    - SIGALARM
### Why disable Interrupts or Signals

![disableis](images/disableis.png)

Here PC: First instruction in handler<br>
SP : thread stack

To prevent deadlock,

1. Keep handler code simple
	- avoid mutex
    - **\-** too restrictive
2. Control interruptions by handler code
	- Use interrupt/signal masks
    - 0011100110.. (0: disabled, 1: enabled)
    
```
clear_field_in_mask(mask)
lock(mutex)
{

#disabled => remaining pending

}
unlock(mutex)
reset_field_in_mask(mask)

#enabled => execute handler code
```

- Interrupt masks are per CPU 
	- if mask disables interrupt, hardware interrupt rounting mechanism will not deliver interrupt
    
- Signal are per execution context (User-level thread on top of Kernel-level thread)
	- if mask disables signal, kernel sees mask and will not interrupt corresponding thread
    
### Types of Signals

1. One-shot Signals
	- "n signals pending == 1 signal pending" : atleast once 
    - must be explicitly re-enabled
2. Realtime Signals 
	- "if n signals raised, then handler is called n times"
    
### Handling interrupts as threads

![interruptsasthreads](images/interruptsasthreads.png)

but dynamic thread creation is expensive!

- Dynamic decision
	- if handler doesn't lock
    	- execute on interrupted threads stack
	- if handler can block
    	- turn into real thread        
- Optimization        
    - pre-create and pre-initialize thread structure for interrupt routines
    
### Threads and Signal Handling

![tshandling](images/tshandling.png)

**Case 1 :**

* User-Level-Thread mask = 1
* Kernel-Level-Thread mask = 1

![case1](images/case1.png)

**Case 2 :**

* User-Level-Thread mask = 0
* Kernel-Level-Thread mask = 1
* another User-Level-Thread mask = 1

![case2](images/case2.png)

**Case 3 :**

* User-Level-Thread mask = 0
* Kernel-Level-Thread mask = 1
* another User-Level-Thread mask = 1
* another Kernel-Level-Thread mask = 1

![case3](images/case3.png)

**Case 4 :**

* User-Level-Thread mask = 0
* Kernel-Level-Thread mask = 1
* all User-Level-Thread mask = 0

![case4](images/case4.png)

**Optimize common case**

- signals less frequennt than signal mask updates
- system calls avoided
	- cheaper to update user-level mask
-  signal handling more expensive    

## Multi-processing vs Multi-threading

How to best provide concurrency?

### Multi-Processing (MP)

**Advantages**<br> 

* Simple programming

**Disadvantages**<br> 

* High memory usage
* Costs context switch
* costly to maintain shared state (tricky port setup)

### Multi-Threading (MP)

**Advantages**<br> 

* Shared address space
* Shared state (no sys calls to other threads)
* Cheap context switch

**Disadvantages**<br> 

* Complex implementation
* Requires synchronization
* Requires underlying support for threads

## Event Driven model

![eventdrivenmodel](images/eventdrivenmodel.png)

Features:

* Single address space
* Single process
* Single thread of control

Dispatcher : acts as a state machine and accepts any external events

When call handler => jump to code

The handler:

* Runs to completion
* if they need to block
	- initiate blocking operation and pass control to dispatch loop
    
### Concurrent execution in Event-driven models

- MP & MT :  1 request per execution context (process/thread)
- Event Driven : Many requests interleaved in an execution context
- Single thread switches among processing of different requests
- Process requests until wait is necessary
	- then switch to another request
    
**Advantages**<br>    

* Single address space
* Single flow of control
* Smaller memory requirement
	- Event Driven model requires less memory than Boss-workers/Pipeline model, where the extra memory is required for helper thread for concurrent blocking I/O not for all concurrent requests.
* No context switches
* No synchronization

**Disadvantages**<br>    

* A blocking request/handler will block entire process

### Asynchronous I/O operations

Asynchronous I/O operations fit well with Event-driven models	

Since asynchronous calls are not easily avalible, helpers can be used to implement the async call functionality:

- designated for blocking I/O operations only
- pipe/socket based communication with event dispatcher
	- select()/ poll() still okay
- helper blocks, but main event loop (& process) will not

### Asymmetric Multi-Process Event Driven model (AMPED & AMTED)

**Advantages**<br>

* Resolve portability limitations of basic event driven model
* Smaller footprint than regular worker thread

**Disadvantages**<br>

* Applicability to certain classes of applications
* Event routing on multi CPU systems

Eg [Apache Web Server](https://en.wikipedia.org/wiki/Apache_HTTP_Server)

![apachewebserver.png](images/apachewebserver.png)

* Core : basic server skeleton
* Modules : per functionality
* Flow of Control : Similar to Event Driven model
* But its an combination of MP + MT,
	- each process = boss/worker with dynamic thread pool
    - number of processes can also be dynamically adjusted
    


> Інформацію для статті було взято та перекладено на українську з репозиторію GitHub [Aniruddha Tapas](https://github.com/Aniruddha-Tapas). Оригінал статті за посиланням - https://github.com/Aniruddha-Tapas/Operating-Systems-Notes