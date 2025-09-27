# b-i-9
🔹 Mục tiêu

Đọc giá trị từ biến trở (cắm vào PA0) bằng ADC1.

Sử dụng DMA1 Channel1 để tự động ghi 1000 mẫu vào RAM.

Trung bình 1000 mẫu → chuyển sang điện áp (mV).

Gửi giá trị điện áp qua USART1 để hiển thị trên PC (dùng Hercules hoặc TeraTerm).

🔹 Phần cứng cần có

STM32F103C8T6 (Bluepill).

Biến trở 10kΩ (hoặc 5k–20k).

USB-UART (PL2303, CH340, CP2102…).

Dây nối.

Kết nối

Biến trở:

Một chân ngoài → 3.3V

Chân ngoài còn lại → GND

Chân giữa (wiper) → PA0 (ADC1_IN0)

UART:

PA9 (TX) → RX của USB-UART

PA10 (RX) → TX của USB-UART (nếu cần nhận)

GND STM32 ↔ GND USB-UART

🔹 Phần mềm

IDE: Keil uVision5

Thư viện: STM32F10x Standard Peripheral Library

Cấu hình UART1 (115200-8-N-1)
USART_InitStructure.USART_BaudRate = 115200;
USART_InitStructure.USART_WordLength = USART_WordLength_8b;
USART_InitStructure.USART_StopBits = USART_StopBits_1;
USART_InitStructure.USART_Parity = USART_Parity_No;
USART_InitStructure.USART_Mode = USART_Mode_Tx | USART_Mode_Rx;

Cấu hình ADC1 + DMA

PA0 chế độ analog input.

ADC1 chạy continuous mode.

DMA1 Channel1 copy dữ liệu từ ADC1->DR vào buffer adc_buffer[1000].

Mode Circular → DMA chạy liên tục, luôn ghi đè buffer.

Xử lý giá trị
sum = 0;
for (i = 0; i < ADC_BUF_LEN; i++) {
    sum += adc_buffer[i];
}
avg = sum / ADC_BUF_LEN;
voltage = (avg * 3300) / 4095;  // chuyển sang mV

🔹 Kết quả

Mở Hercules (Serial, COMx, Baud 115200) → màn hình sẽ in ra:

Voltage: 1234 mV

Voltage: 2500 mV

Voltage: 3300 mV


Khi vặn biến trở, giá trị thay đổi từ ~0 mV đến ~3300 mV.
