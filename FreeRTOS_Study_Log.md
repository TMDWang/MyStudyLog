# FreeRTOS Study Log

### 0 简介

**在FreeRTOS中，每一个执行的线程被叫做任务**

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







