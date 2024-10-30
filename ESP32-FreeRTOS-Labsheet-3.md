# การทดลอง ESP32 FreeRTOS 
##  ฟังก์ชัน xTaskCreatePinnedToCore()

1. แก่ไข Code จากโปรแกรมที่แล้ว โดยการเพิ่ม task เข้าไปอีก 1 task (ดู comment ใน code)



```c
#include <stdio.h>
#include <stdbool.h>
#include <unistd.h>

#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

TaskHandle_t MyFirstTaskHandle = NULL;
// 1. เพิ่ม MySeconeTaskHandle
TaskHandle_t MySeconeTaskHandle = NULL;

void My_First_Task(void * arg)
{
	uint16_t i = 0;
	while(1)
	{
		printf("Hello My First Task %d\n",i);
		vTaskDelay(1000/portTICK_PERIOD_MS);
		i++;
	}
}

// 2. เพิ่ม task function
void My_Second_Task(void * arg)
{
	uint16_t i = 0;
	while(1)
	{
		printf("Hello My Second Task %d\n",i);
		vTaskDelay(1000/portTICK_PERIOD_MS);
		i++;
	}
}


void app_main(void)
{
	xTaskCreate(My_First_Task, "Fitst_Task", 4096, NULL, 10, &MyFirstTaskHandle);
	// 3. สร้างและเรียกใช้ task
	xTaskCreate(My_Second_Task, "Second_Task", 4096, NULL, 10, &MySeconeTaskHandle);
}
```

2. รันและบันทึกผลจากโปรแกรมข้างบน

## ![image](https://github.com/user-attachments/assets/bc559dcf-0685-40f6-b5a8-9ad2abde2234)
## ขึ้น My First Task และ My Second Task พร้อมกันและนับเลข

3.  แก้ไข code ในส่วนของการสร้าง task 2 (ตามหมายเหตุหมายเลข 3) เป็นดังนี้

```c
void app_main(void)
{
	xTaskCreate(My_First_Task, "Fitst_Task", 4096, NULL, 10, &MyFirstTaskHandle);
	// สร้างและเรียกใช้ task ด้วยฟังก์ชัน xTaskCreatePinnedToCore
	xTaskCreatePinnedToCore(My_Second_Task, "Second_Task", 4096, NULL, 10, &MySeconeTaskHandle, 1);
}
```

4. รันและบันทึกผลจากโปรแกรมข้างบน ได้ผลเหมือนหรือต่างกันอย่างไร

## ![image](https://github.com/user-attachments/assets/9cf22e20-339c-47a1-a6b4-a20d52df1dec)

## ได้ผลเหมือนกันครับแต่ task ถูกสร้างกันคนตัว

--------
### หมายเหตุ API ของ xTaskCreatePinnedToCore
``` c
/**
 * @brief Create a new task that is pinned to a particular core
 *
 * This function is similar to xTaskCreate(), but allows the creation of a pinned
 * task. The task's pinned core is specified by the xCoreID argument. If xCoreID
 * is set to tskNO_AFFINITY, then the task is unpinned and can run on any core.
 *
 * @note If ( configNUMBER_OF_CORES == 1 ), setting xCoreID to tskNO_AFFINITY will be
 * be treated as 0.
 *
 * @param pxTaskCode Pointer to the task entry function.
 * @param pcName A descriptive name for the task.
 * @param ulStackDepth The size of the task stack specified as the NUMBER OF
 * BYTES. Note that this differs from vanilla FreeRTOS.
 * @param pvParameters Pointer that will be used as the parameter for the task
 * being created.
 * @param uxPriority The priority at which the task should run.
 * @param pxCreatedTask Used to pass back a handle by which the created task can
 * be referenced.
 * @param xCoreID The core to which the task is pinned to, or tskNO_AFFINITY if
 * the task has no core affinity.
 * @return pdPASS if the task was successfully created and added to a ready
 * list, otherwise an error code defined in the file projdefs.h
 */
    BaseType_t xTaskCreatePinnedToCore( TaskFunction_t pxTaskCode,
                                        const char * const pcName,
                                        const uint32_t ulStackDepth,
                                        void * const pvParameters,
                                        UBaseType_t uxPriority,
                                        TaskHandle_t * const pxCreatedTask,
                                        const BaseType_t xCoreID );
```



## [>> ต่อไป >>](./ESP32-FreeRTOS-Labsheet-4.md) 
