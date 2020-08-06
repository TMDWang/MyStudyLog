# FreeRTOS Study Log

### 0 简介

**在FreeRTOS中，每一个执行的线程被叫做任务**

**内核对象包括任务、队列、信号量、事件组**

##### 0.1 一些术语的中英对照

|    English     |   中文   |
| :------------: | :------: |
| portable layer | 可移植层 |
|                |          |
|                |          |
|                |          |
|                |          |
|                |          |
|                |          |
|                |          |
|                |          |



### 1 数据类型 & 编码风格

**Tab键=4个空格。**

**对于源代码中过多类型转换的说明**

FreeRTOS源代码可以用许多不同的编译器进行编译，所有这些编译器在生成警告的方式和时间上都是不同的。特别是，不同的编译器希望以不同的方式使用类型转换。因此，FreeRTOS源代码中包含的类型转换比通常需要的要多。

#### 1.1 TickType_t & BaseType_t

FreeRTOS配置一个周期性的中断，称为tick interrupt。从FreeRTOS应用启动开始tick interrupt发生的次数称为tick count。tick count被用来度量时间。两个tick interrupt之间的用时称为tick period。TickType_t是用来保存tick count值的数据类型，即指定时间。TickType_t可以是uint16或者uint32，这取决于FreeRTOSConfig.h中变量configUSE_16_BIT_TICKS的值，设置为1则使用uint16，0为uint32，在32位处理器上没有理由使用uint16。

BaseType_t通常用作函数返回值的类型，一般是很有限的值，常用于pdTRUE/pdFALSE的布尔运算。BaseType_t的类型一般取决于处理器的位数，8-bit、16-bit、32-bit。

#### 1.2 变量命名规则

FreeRTOS中使用前缀（prefix）告诉你该变量的数据类型。具体如下表所示。

| 前缀（prefix） |    数据类型    | 示例 |
| :------------: | :------------: | :--: |
|      "c"       |      char      |      |
|      "s"       | int16_t(short) |      |
|      "l"       | int32_t(long)  |      |
|      "x"       |   BaseType_t   |      |

"x"前缀也加在非标准的数据类型前面，例如structures、task handles、queue handles等。

另外，如果变量是unsigned也会加前缀"u"，如果变量是指针类型会加"p"。例如，一个uint8_t类型的变量会加前缀"uc"，指向字符类型的指针会加前缀"pc"。

#### 1.3 函数命名规则

函数的前缀中有该函数返回值的类型和定义该函数的文件。如下表：

|       函数名        | 含义                                                |
| :-----------------: | :-------------------------------------------------- |
|    xQueueReceive    | 返回值类型为**BaseType_t**，在**queue.c**中定义     |
| vTaskPrioritySet()  | 返回值类型为**void**，在**task.c**中定义            |
| pvTimerGetTimerID() | 返回值类型为**指向void的指针**，在**timer.c**中定义 |

另外，前缀"prv"表示该函数为定义该函数的文件私有的（private），即函数的作用域为该文件内。

#### 1.4 宏命名规则

大部分的宏都用大写字母，并且加上小写字母前缀表明定义该宏的位置，如下表所示。

| 前缀                               |     定义该宏的位置      |
| :--------------------------------- | :---------------------: |
| port(例如，portMAX_DELAY)          | portable.h或portmacro.h |
| task(例如，taskENTER_CRITICAL())   |         task.h          |
| pd(例如，pdTRUE)                   |       projdefs.h        |
| config(例如，configUSE_PREEMPTION) |    FreeRTOSConfig.h     |
| err(例如，errQUEUE_FULL)           |       projdefs.h        |

**注意**：虽然信号量应用程序接口（semaphore API）几乎全部使用宏来编写，但其命名按照函数的命名规则，而不是宏的命名规则。

下表中的宏在整个FreeRTOS源代码中都可以使用。

|   宏    |  值  |
| :-----: | :--: |
| pdTRUE  |  1   |
| pdFALSE |  0   |
| pdPASS  |  1   |
| pdFAIL  |  0   |

### 2 堆内存管理

FreeRTOS中使用pvPortMalloc()和pvPortFree()代替C标准库中的malloc()和free()函数，它们的函数原型（prototype）是一样的。FreeRTOS提供了五种pvPortMalloc()函数和pvPortFree()函数的实现，用户编写的FreeRTOS应用可以使用其中的一种，或者使用自己提供的。

Heap_1.c中只实现了一个非常基础的pvPortMalloc()版本，没有实现pvPortFree()。因此适用于从不删除任务或者内核对象的应用。

![image-20200804232247161](FreeRTOS_illustration/image-20200804232247161.png)

Heap_4.c相当于Heap_2.c的增强版，在设计中推荐使用前者，保留后者是为了向下兼容。相比于Heap_1.c，Heap_2.c中允许释放内存。Heap_2.c不能像Heap_4.c可以把相邻的小内存块合并成大内存块，因此会产生内存碎片，不过它适用于重复创建删除占用相同内存大小任务的应用中。

![image-20200804232329557](FreeRTOS_illustration/image-20200804232329557.png)

Heap_3.c使用标准库中的malloc() & free()函数，因此堆内存的大小由连接器配置决定，不受configTOTAL_HEAP_SIZE设置的影响。Heap_3.c通过暂时挂起FreeRTOS调度器保证malloc() & free()函数thread-safe。

Heap_4.c同Heap_1.c和Heap_2.c一样将内存分为小块，且被静态分配。如前所述，Heap_4.c可以将临近的内存合并到一起，因此它适用于任务被重复创建和删除的应用，任务所占内存大小可不相同。

![image-20200804233951224](FreeRTOS_illustration/image-20200804233951224.png)

Heap_5.c中使用的内存分配与释放算法与Heap_4.c相同，但它不局限于分配单个静态声明的内存区域，它可以从多块分离的内存空间中分配内存。当FreeRTOS运行在系统内存映射不是一个连续的内存块是，它是相当有用的。

（只有）在使用Heap_5.c内提供的pvPortMalloc()函数时，必须提前明确地使用vPortDefineHeapRegions()函数初始化所使用的内存分配方案。在创建任何的内核对象（任务，队列，信号量等）之前必须先调用vPortDefineRegions()，该函数用来指定每一个内存块的开始地址和其大小，所有的内存块供Heap_5.c使用。（更详尽的说明尽在内核手册）

![image-20200806234149698](FreeRTOS_illustration/image-20200806234149698.png)

### 3 任务管理

**任务**用普通的C函数实现，唯一特殊的是他们的函数原型，必须返回void，必须给分空指针void *。例如下面：

```
void ATaskFunction(void *pvParameters);
```

**每个任务本身都是一个小程序，它有一个入口点，通常会一直运行在死循环中，并永远不会退出。**FreeRTOS任务绝不允许从它的实现函数中以任何方式退出，不能包含return语句，不允许在函数结束时运行。如果任务不再需要了，可以删除它。下图为创建任务的例子。

![image-20200807002419298](FreeRTOS_illustration/image-20200807002419298.png)

























