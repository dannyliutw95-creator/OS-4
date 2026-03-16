# OS-4
本專案旨在展示如何利用 FreeRTOS (Real-Time Operating System) 在 Raspberry Pi Pico 上實現「多任務並行處理」。不同於傳統的 loop() 循序執行，本程式將每個 LED 的閃爍邏輯封裝成獨立的任務（Task），讓它們看似同時運行，並能精確控制彼此的啟動延遲與優先級。

🛠 核心技術架構
RTOS 任務調度 (Task Scheduling)：

使用 xTaskCreate 或專案封裝的 start() 方法來建立多個並行任務。

每個 LED 都有自己的堆疊空間與執行週期，互不干擾。

物件導向封裝 (C++ Class)：

BlinkAgent 類別：這是本專案的核心。它將 GPIO 初始化、任務建立、閃爍邏輯（亮/暗/延遲）全部封裝在一個類別中。

透過建立多個 BlinkAgent 實例（例如 led0 到 led8），可以輕鬆管理多個硬體引腳。

非阻塞延遲 (Non-blocking Delay)：

程式中使用 vTaskDelay() 而非 sleep_ms()。

關鍵差異：vTaskDelay 會讓出 CPU 使用權給其他優先級高的任務，極大地提高了處理器的效率。

邏輯流程說明
在 main.cpp 的 mainTask 中，程式依序執行以下步驟：

初始化硬體：設定 GPIO 引腳模式。

啟動板載 LED：首先啟動 led0 (GPIO 25)，作為系統運行狀態指示燈。

序列啟動任務：

程式會依序呼叫 led1.start(), led2.start() ... 等。

在每個啟動指令之間加入短暫的 vTaskDelay(100)，這創造了你在影片或實體上看到的「逐個亮起」的啟動動畫效果。

優先級管理：

透過 TASK_PRIORITY + n 設定不同的任務優先級，確保系統在處理多個變換時能維持穩定。
