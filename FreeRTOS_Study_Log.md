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

**BaseType_t**通常用作函数返回值的类型，一般是很有限的值，常用于pdTRUE/pdFALSE的布尔运算。BaseType_t的类型一般取决于处理器的位数，8-bit、16-bit、32-bit。

#### 1.2 变量命名规则

FreeRTOS中使用前缀（prefix）告诉你该变量的数据类型。具体如下表所示。

| 前缀（prefix） |    数据类型    | 示例 |
| :------------: | :------------: | :--: |
|      "c"       |      char      |      |
|      "s"       | int16_t(short) |      |
|      "l"       | int32_t(long)  |      |
|      "x"       |   BaseType_t   |      |

**"x"前缀也加在非标准的数据类型前面，例如structures、task handles、queue handles等。**

另外，**如果变量是unsigned也会加前缀"u"，如果变量是指针类型会加"p"**。例如，一个uint8_t类型的变量会加前缀"uc"，指向字符类型的指针会加前缀"pc"。

#### 1.3 函数命名规则

函数的前缀中有该函数返回值的类型和定义该函数的文件。如下表：

|         函数名          | 含义                                                |
| :---------------------: | :-------------------------------------------------- |
|    **x**QueueReceive    | 返回值类型为**BaseType_t**，在**queue.c**中定义     |
| **v**TaskPrioritySet()  | 返回值类型为**void**，在**task.c**中定义            |
| **pv**TimerGetTimerID() | 返回值类型为**指向void的指针**，在**timer.c**中定义 |

另外，**前缀"prv"表示该函数为定义该函数的文件私有的（private），即函数的作用域为该文件内。**

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

#### 3.1 任务状态（Task States）

一个应用程序可以由许多任务组成。单核处理器运行应用程序时在任一时刻只有一个任务可以执行。这意味着任务可以有运行（Running）和非运行（Not Running）两个状态。

![image-20200807165136069](FreeRTOS_illustration/image-20200807165136069.png)

任务从非运行态转化到运行态时称为"switched in"或者"swapped in"。相反的，任务宠运行态转化到非运行态称为"switched out"或"swapped out"。**FreeRTOS scheduler（调度器）是唯一可以转换任务状态的实体**。

#### 3.2 创建任务

**xTaskCreate()函数**

xTaskCreate()函数用于创建任务（此函数可能是最复杂的API Functions），在FreeRTOS V9.0.0中还包含xTaskCreateStatic()函数，该函数在编译时为任务的创建静态分配内存。xTaskCreate()函数原型如下图所示：

![image-20200808035645183](FreeRTOS_illustration/image-20200808035645183.png)

**参数**

**pvTaskCode**：任务是简单的C函数，通常实现为一个死循环。pvTaskCode参数为指向该任务的指针（实际上，就是任务的函数名）。

**pcName**：任务的一个描述。在FreeRTOS中不会用到它，他的存在纯粹是为了debugging目的。通过一个可读的名字识别任务相对于通过任务句柄识别的有点是显而易见的。configMAX_TASK_NAME_LEN定义了该名字可以占用的最大长度（包含结束符），如果超过最大值，会被切掉。

**usStackDepth**：任务创建时，内核会为每一个任务都分配一个属于该任务唯一的栈，usStackDepth参数告诉内核该任务需要的栈大小，单位是字words。常量configMINIMAL_STACK_SIZE规定了空闲任务使用的栈大小，该常量也是所有任务栈大小的最小值。

**pvParameters**：任务接受一个指向void的指针。pvParameters的值就是传入任务的值。

**uxPriority**：任务执行的优先级，优先级可以设置为最低的0到最高优先级（configMAX_PRIORITIES - 1），常量configMAX_PRIORITIES由用户定义。如果给定的优先级数值大于（configMAX_PRIORITIES - 1），则会将其优先级设置为最大优先级。

**pxCreatedTask**：pxCreatedTask参数用作当前创建任务的句柄。该句柄可以用来改变该任务的优先级，或删除该任务。如果你的应用程序中用不上该句柄，可将其设置为NULL。

**返回值**

返回值可能是**pdPASS**，表示任务创建成功，也可能是**pdFAIL**，表示任务创建失败，原因是内存不足。

#### 3.3 任务优先级 Task Priorities（重点）

在调度器启动之后，可以通过vTaskPrioritySet()函数改变创建任务时给定的初始任务优先级。任务优先级的最大值configMAX_PRIORITIES在FreeRTOSConfig.h文件中定义，编译时配置为常量。数字越小优先级越低，0代表最低优先级。多个任务可以有相同的优先级，确保设计的灵活性最大。

**FreeRTOS调度器可以使用以下两种方法中的一种来决定哪一个任务处于运行态。configMAX_PRIORITIES的取值由使用的决策方法决定。（可以理解为切换任务的代码？）**

**通用方法**

通用方法用C代码实现，可以被FreeRTOS的所有架构端口使用。通用方法对configMAX_PRIORITIES的最大取值没有限制，但强烈推荐其取值够用就好，过大的优先级会浪费RAM，最坏情况下的执行时间也会加长。

当FreeRTOSConfig.h文件中configUSE_PORT_OPTIMISED_TASK_SELECTION被设置为0或未定义（undefined）时，使用通用方法。另外如果FreeRTOS port只提供该方法，则只能用它。

**结构优化方法**

结构优化方法使用一小段汇编语言，比通用方法更快。configMAX_PRIORITIES的设置不影响最坏情况下的执行时间。但使用结构优化方法时，改值的大小不能超过32。同样也强烈建议够用就好，过大，依然或浪费RAM。

当FreeRTOSConfig.h文件中configUSE_PORT_OPTIMISED_TASK_SELECTION设置为1时，将会使用该方法，不是所有的FreeRTOS ports都提供一个结构优化方法。

FreeRTOS调度器会确保当前可运行的任务中任务优先级最高的进入运行状态，如果由多个任务处于最高优先级，则调度器将轮流运行它们。

#### 3.4 时间度量 & Tick中断（重点）

在后面的调度算法章节中将会讲述被称为时间片的可选特征。到目前位置，前面的例子（在书中，我没摘抄下来）中都使用的时时间片，从他们的输出中可以观察到时间片的使用。在例子中，两个任务创建时拥有相同的优先级，都可以运行。因此，每个任务运行一个时间片的时间，在时间片开始时进入运行态，在时间片结束时退出运行态。在下图中，t1到t2之间的时间长度为一个时间片。

![image-20200808165422689](FreeRTOS_illustration/image-20200808165422689.png)

**为了能够选择下一个要执行的任务，调度器本身必须在每个时间片结束时运行。被称为tick interrupt的周期中断，就是为了此目的。**时间片的长度由tick终端频率决定，该常量configTICK_RATE_HZ的值在FreeRTOSConfig.h文件中定义，编译时配置完成。举个栗子，如果**configTICK_RATE_HZ**设置为100，则时间片的长度为10毫秒。两次tick中断之间的长度称为tick周期，其就等于一个时间片的长度。

**注意**：调度器选择新任务的执行，不只是在时间片结束时，在当前任务处于阻塞状态或者当中断移动一个更高优先级的任务进入就绪态时，调度器会立即选择一个新的任务去执行。

configTICK_RATE_HZ的值取决于你设计的应用程序，一般取值为100。

调用FreeRTOS应用程序接口时指定的时间总是滴答周期的倍数，该时间经常被叫做“ticks”。pdMS_TO_TICKS()宏可以将以毫秒为单位的时间转换为ticks周期数。转换的精度取决于定义的滴答频率configTICK_RATE_HZ，当滴答频率大于1000时该宏不能被使用。使用该宏指定时间的优点是，当tick频率改变时指定的时间仍然是xx毫秒。

#### 3.5 详述非运行状态

之前例子（在手册中，未摘抄）中，任务不需要等待什么东西，因此它将经常可以进如运行态，但实际上这种连续性任务是没多大用处的，这种任务的运行会阻碍比它优先级低的任务进入运行态。











#### 3.x 调度算法

