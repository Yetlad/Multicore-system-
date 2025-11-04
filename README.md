Here is a **README.md** file for your ESP32 Multicore Demo code.

```markdown
# 💻 ESP32 Multicore Demo

This project is a demonstration of running multiple **FreeRTOS tasks** on different cores of a dual-core **ESP32** microcontroller. It uses the `xTaskCreatePinnedToCore` function to explicitly assign tasks to specific cores, showcasing basic multicore programming concepts.

---

## ✨ Features

* **Multicore Execution:** Demonstrates how to create and assign separate tasks (`Task L` and `Task H`) to different cores (`Core 0` and `Core 1`) of the ESP32.
* **Task Pinned to Core:** Uses the `xTaskCreatePinnedToCore` FreeRTOS function.
* **CPU Hogging Simulation:** Each task includes a simulated busy-wait loop (`hog_delay`) to monopolize its assigned CPU core for a set duration, illustrating concurrent execution and resource utilization.
* **Core ID Reporting:** Each task reports its name and the core it is currently running on via the Serial Monitor.

---

## 🛠️ Requirements

* **Hardware:** An **ESP32** development board.
* **Software:**
    * Arduino IDE or PlatformIO.
    * The ESP32 board package installed.

---

## ⚙️ Configuration

The core definitions and the "hogging" time are configured at the top of the sketch:

* **`pro_cpu`**: Defined as `0`. This is typically the **Protocol CPU** (Core 0), often used for the Wi-Fi/Bluetooth stack and FreeRTOS kernel tasks.
* **`app_cpu`**: Defined as `1`. This is typically the **Application CPU** (Core 1), often reserved for user application tasks.
* **`time_hog`**: Set to `200` milliseconds. This is the approximate duration each task will spend "hogging" its assigned CPU core in a busy-wait loop.

> **Note:** Although the code defines `app_cpu = 1`, both `Task L` and `Task H` are currently pinned to the `pro_cpu` (`Core 0`) with different priorities. This setup will demonstrate the FreeRTOS scheduler prioritizing `Task H` over `Task L` on the **same core**. To truly run them on *different* cores, one of the `xTaskCreatePinnedToCore` calls should use `app_cpu`.

---

## 🚀 Usage

1.  Open the sketch in your Arduino IDE or PlatformIO project.
2.  Select your ESP32 board and the correct COM port.
3.  Upload the code.
4.  Open the **Serial Monitor** at **115200 baud**.

You will see continuous output from both **Task L** and **Task H**. Since both tasks are currently pinned to **Core 0** (`pro_cpu`) with different priorities (`Task H` priority 2, `Task L` priority 1), the output will reflect how the FreeRTOS scheduler manages these tasks concurrently on a single core, favoring the higher-priority `Task H`.

**Example Output:**

```

\---FreeRTOS Priority Inheritance Demo---
Task H, Core 0
Task H, Core 0
Task H, Core 0
Task L, Core 0
Task H, Core 0
...

````

### Multicore Execution Modification

To fully utilize both cores as the demo's initial comment suggests, change the core affinity for one of the tasks in the `setup()` function. For example, to run `Task L` on `Core 1`:

```cpp
// Original setup for Task L (pinned to pro_cpu)
xTaskCreatePinnedToCore(doTaskL,
                        "Task L",
                        2048,
                        NULL,
                        1,
                        NULL,
                        pro_cpu); // Change this to app_cpu for true dual-core demo
````

If you modify `Task L` to run on `app_cpu` (Core 1) and keep `Task H` on `pro_cpu` (Core 0), the output will show:

```
Task H, Core 0
Task L, Core 1
Task H, Core 0
Task L, Core 1
Task H, Core 0
Task L, Core 1
...
```

-----

## ⚠️ Important Note on `hog_delay()`

The function `hog_delay()` uses a **busy-wait** loop (`asm("nop");`). This technique is highly discouraged in production FreeRTOS applications as it prevents the operating system from scheduling other tasks effectively and wastes CPU cycles. For proper task delay, always use a FreeRTOS function like `vTaskDelay()`. The busy-wait is included here purely for an educational demonstration of resource contention.

-----

## 📜 License

This code is released under the **0BSD License**.

```

Would you like me to adjust the task pinning in the code to ensure both cores are utilized for a true multicore demonstration?
```
