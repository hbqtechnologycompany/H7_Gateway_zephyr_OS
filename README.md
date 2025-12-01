# Tài Liệu Mô Tả Dự Án - iGATEWAY H7 với Zephyr OS

## 1. Tổng Quan Dự Án

### 1.1. Mục Tiêu
Phát triển firmware cho thiết bị iGATEWAY H7 dựa trên nền tảng Zephyr OS, tích hợp các chức năng chính:
- Màn hình LCD 7 inch với giao diện người dùng
- Kết nối mạng LTE 4G
- Kết nối WiFi
- Quản lý và điều khiển các ngoại vi I/O

### 1.2. Phần Cứng
- **MCU**: STM32H7 Series
- **Hệ điều hành**: Zephyr RTOS
- **Màn hình**: LCD 7 inch
- **Kết nối mạng**: 4G LTE Module + WiFi Module
- **Bộ nhớ**: SDRAM + QSPI Nor Flash (hoặc NAND Flash + QSPI RAM)

## 2. Các Chức Năng Cần Phát Triển

### 2.1. Màn Hình LCD 7 Inch ⭐ **ƯU TIÊN CAO**

#### Yêu Cầu:
- **Giao diện**: LCD Interface (LTDC - LCD-TFT Display Controller)
- **Độ phân giải**: 800x480 hoặc tương đương (7 inch)
- **Touch Screen**: Hỗ trợ cảm ứng (nếu có)

#### Công Việc Phát Triển:
1. **Driver LCD**:
   - Cấu hình LTDC controller của STM32H7
   - Khởi tạo màn hình LCD
   - Quản lý frame buffer
   - Hỗ trợ double buffering (nếu cần)

2. **Graphics Stack**:
   - Tích hợp LVGL hoặc GUI framework tương tự
   - Quản lý layers và windows
   - Hỗ trợ fonts và images
   - Animation và transitions

3. **Touch Screen Driver** (nếu có):
   - Driver cho touch controller
   - Calibration
   - Gesture recognition

4. **UI Application**:
   - Giao diện chính
   - Menu navigation
   - Hiển thị thông tin trạng thái
   - Cấu hình hệ thống

#### Zephyr Components Cần Sử dụng:
- `CONFIG_DISPLAY`
- `CONFIG_LVGL` (nếu dùng LVGL)
- `CONFIG_STM32_LTDC`
- `CONFIG_INPUT` (cho touch screen)

---

### 2.2. Kết Nối LTE 4G ⭐ **ƯU TIÊN CAO**

#### Yêu Cầu:
- **Module**: 4G LTE Module (quan sát từ schematic)
- **Giao tiếp**: UART/AT Commands
- **Chức năng**: 
  - Kết nối mạng di động
  - Data connection (TCP/IP)
  - SMS (nếu cần)
  - GPS (nếu module hỗ trợ)

#### Công Việc Phát Triển:
1. **4G Module Driver**:
   - UART driver cho module
   - AT command parser
   - Power management (ON/OFF, reset)
   - SIM card detection

2. **Network Stack Integration**:
   - PPP (Point-to-Point Protocol) driver
   - Tích hợp với Zephyr network stack
   - IP address management
   - DNS resolution

3. **Connection Management**:
   - Auto-connect/reconnect
   - Signal strength monitoring
   - Network registration status
   - Data usage tracking

4. **Application Layer**:
   - HTTP/HTTPS client
   - MQTT client (nếu cần)
   - Custom protocols

#### Zephyr Components Cần Sử dụng:
- `CONFIG_UART`
- `CONFIG_MODEM`
- `CONFIG_NET_PPP`
- `CONFIG_NETWORKING`
- `CONFIG_NET_IPV4` hoặc `CONFIG_NET_IPV6`
- `CONFIG_NET_SOCKETS`

---

### 2.3. Kết Nối WiFi ⭐ **ƯU TIÊN CAO**

#### Yêu Cầu:
- **Module**: WiFi Module (từ WIFI BLOCK)
- **Giao tiếp**: SPI/SDIO hoặc UART
- **Chức năng**:
  - WiFi Station mode
  - WiFi Access Point mode (nếu cần)
  - WPA2/WPA3 security
  - DHCP client/server

#### Công Việc Phát Triển:
1. **WiFi Module Driver**:
   - SPI/SDIO/UART driver tùy module
   - Module initialization
   - Firmware loading (nếu cần)

2. **WiFi Stack Integration**:
   - Tích hợp với Zephyr WiFi stack
   - WLAN driver interface
   - Scan và connect
   - Power management

3. **Network Configuration**:
   - WiFi credentials management
   - Multiple network profiles
   - Auto-connect to saved networks
   - Signal strength monitoring

4. **Security**:
   - WPA2/WPA3 support
   - Certificate management (nếu cần)

#### Zephyr Components Cần Sử dụng:
- `CONFIG_WIFI`
- `CONFIG_NET_L2_WIFI_MGMT`
- `CONFIG_NET_MGMT`
- `CONFIG_NETWORKING`

---

### 2.4. Các Ngoại Vi I/O

#### 2.4.1. CAN Interface (2x CAN)
- **Yêu cầu**: 2 kênh CAN độc lập
- **Công việc**:
  - CAN driver cho STM32H7
  - CAN protocol stack
  - Message filtering
  - Error handling và recovery

- **Zephyr Components**:
  - `CONFIG_CAN`
  - `CONFIG_CAN_STM32`

#### 2.4.2. Modbus Interface (1x Modbus)
- **Yêu cầu**: Modbus RTU hoặc Modbus TCP
- **Công việc**:
  - Modbus RTU over UART
  - Modbus TCP over Ethernet/WiFi/4G
  - Modbus stack implementation
  - Register mapping

- **Zephyr Components**:
  - `CONFIG_MODBUS` (nếu có)
  - `CONFIG_UART` (cho RTU)
  - `CONFIG_NET_SOCKETS` (cho TCP)

#### 2.4.3. DAC Output (4x DAC, 0-5VDC)
- **Yêu cầu**: 4 kênh DAC, output 0-5V
- **Công việc**:
  - DAC driver cho STM32H7
  - Voltage scaling (0-5V)
  - Calibration
  - Application API

- **Zephyr Components**:
  - `CONFIG_DAC`
  - `CONFIG_DAC_STM32`

#### 2.4.4. ADC Input (8x ADC, 0-12VDC)
- **Yêu cầu**: 8 kênh ADC, input 0-12V
- **Công việc**:
  - ADC driver cho STM32H7
  - Voltage divider/scaling (0-12V to ADC range)
  - Sampling và filtering
  - Calibration

- **Zephyr Components**:
  - `CONFIG_ADC`
  - `CONFIG_ADC_STM32`

#### 2.4.5. Relay Output (8x Relay 2A)
- **Yêu cầu**: 8 relay, mỗi relay 2A
- **Công việc**:
  - GPIO driver cho relay control
  - Relay state management
  - Safety features (interlock, timeout)
  - Application API

- **Zephyr Components**:
  - `CONFIG_GPIO`
  - `CONFIG_GPIO_STM32`

#### 2.4.6. Opto Input (8x Opto Input)
- **Yêu cầu**: 8 kênh input cách ly quang
- **Công việc**:
  - GPIO driver cho opto inputs
  - Debouncing
  - Edge detection
  - Status monitoring

- **Zephyr Components**:
  - `CONFIG_GPIO`
  - `CONFIG_GPIO_STM32`

#### 2.4.7. PWM Input (4x PWM Input)
- **Yêu cầu**: 4 kênh PWM input capture
- **Công việc**:
  - Timer input capture driver
  - Frequency và duty cycle measurement
  - Edge detection
  - Application API

- **Zephyr Components**:
  - `CONFIG_COUNTER`
  - `CONFIG_COUNTER_STM32`

#### 2.4.8. UART Interface
- **Yêu cầu**: Nhiều cổng UART
- **Công việc**:
  - UART driver configuration
  - Baud rate, parity, stop bits
  - Flow control
  - Application protocols

- **Zephyr Components**:
  - `CONFIG_UART`
  - `CONFIG_UART_STM32`

#### 2.4.9. USB Interface
- **Yêu cầu**: USB device/host
- **Công việc**:
  - USB driver
  - USB device classes (CDC, Mass Storage, etc.)
  - USB host support (nếu cần)

- **Zephyr Components**:
  - `CONFIG_USB`
  - `CONFIG_USB_DEVICE_STACK`
  - `CONFIG_USB_STM32`

---

### 2.5. Bộ Nhớ (SRAM + FLASH)

#### Option 1: SDRAM + QSPI Nor Flash
- **SDRAM**: External SDRAM cho frame buffer và data
- **QSPI Flash**: External Flash cho storage

#### Option 2: NAND Flash + QSPI RAM
- **NAND Flash**: External NAND Flash
- **QSPI RAM**: External RAM

#### Công Việc:
- Memory controller drivers
- File system (LittleFS, FATFS)
- Memory management

#### Zephyr Components:
- `CONFIG_FLASH`
- `CONFIG_FILE_SYSTEM`
- `CONFIG_LITTLEFS` hoặc `CONFIG_FAT_FILESYSTEM_ELM`

---

### 2.6. Power Supply Management

#### Yêu Cầu:
- Power supply block quản lý nguồn
- Power monitoring
- Low power modes (nếu cần)

#### Công Việc:
- PMIC driver (nếu có)
- Power monitoring
- Sleep/wake management

---

## 3. Kiến Trúc Hệ Thống

### 3.1. System Architecture

```
┌─────────────────────────────────────────────────┐
│           Application Layer                     │
│  (UI, Network Apps, I/O Control, Protocols)      │
└─────────────────────────────────────────────────┘
                    │
┌─────────────────────────────────────────────────┐
│           Zephyr OS Layer                        │
│  - Kernel (Threads, IPC, Timers)                │
│  - Device Drivers                               │
│  - Network Stack                                │
│  - File System                                  │
└─────────────────────────────────────────────────┘
                    │
┌─────────────────────────────────────────────────┐
│           Hardware Abstraction Layer (HAL)      │
│  - STM32H7 HAL Drivers                          │
│  - Peripheral Drivers                           │
└─────────────────────────────────────────────────┘
                    │
┌─────────────────────────────────────────────────┐
│           Hardware (STM32H7 + Peripherals)      │
└─────────────────────────────────────────────────┘
```

### 3.2. Thread Architecture

1. **Main Thread**: System initialization
2. **UI Thread**: LCD rendering, touch handling
3. **Network Thread**: 4G/WiFi management
4. **I/O Thread**: ADC, DAC, Relay, Opto, PWM
5. **CAN Thread**: CAN bus communication
6. **Modbus Thread**: Modbus protocol
7. **System Thread**: Power management, watchdog

### 3.3. Inter-Thread Communication

- **Message Queues**: Cho data transfer giữa threads
- **Semaphores**: Cho synchronization
- **Mutexes**: Cho resource protection
- **Events**: Cho event notification

---

## 4. Kế Hoạch Phát Triển

### Phase 1: Foundation (Tuần 1-2)
- [ ] Setup Zephyr development environment
- [ ] Board configuration cho STM32H7
- [ ] Basic system initialization
- [ ] Clock và power configuration
- [ ] UART console working

### Phase 2: Core Drivers (Tuần 3-4)
- [ ] LCD driver và display initialization
- [ ] SDRAM/Flash drivers
- [ ] GPIO drivers
- [ ] ADC/DAC drivers
- [ ] Timer/PWM drivers

### Phase 3: Network Connectivity (Tuần 5-7)
- [ ] 4G Module driver và AT command parser
- [ ] PPP integration
- [ ] WiFi module driver
- [ ] Network stack integration
- [ ] Connection management

### Phase 4: UI Development (Tuần 8-10)
- [ ] LVGL integration
- [ ] Basic UI screens
- [ ] Touch screen driver (nếu có)
- [ ] Status display
- [ ] Configuration screens

### Phase 5: I/O Peripherals (Tuần 11-13)
- [ ] CAN drivers và protocol
- [ ] Modbus implementation
- [ ] Relay control
- [ ] Opto input handling
- [ ] PWM input capture

### Phase 6: Integration & Testing (Tuần 14-16)
- [ ] System integration
- [ ] End-to-end testing
- [ ] Performance optimization
- [ ] Bug fixes
- [ ] Documentation

---

## 5. Các Module Không Cần Phát Triển (Giai Đoạn Hiện Tại)

### 5.1. LoRa Interface
- **Trạng thái**: Không phát triển trong giai đoạn này
- **Lý do**: Chưa cần thiết cho yêu cầu hiện tại

### 5.2. Zigbee Block
- **Trạng thái**: Không phát triển trong giai đoạn này
- **Lý do**: Chưa cần thiết cho yêu cầu hiện tại

---

## 6. Yêu Cầu Kỹ Thuật

### 6.1. Development Environment
- **OS**: Linux (Ubuntu 20.04+) hoặc Windows với WSL
- **Zephyr SDK**: Version 3.x trở lên
- **Toolchain**: GCC ARM Embedded
- **Debugger**: ST-Link hoặc J-Link
- **IDE**: VS Code với Zephyr extension (khuyến nghị)

### 6.2. Zephyr Version
- **Recommended**: Zephyr 3.4.x hoặc mới hơn
- **Board Support**: STM32H7 series boards

### 6.3. Dependencies
- West tool
- Python 3.8+
- CMake 3.20+
- Ninja build system

---

## 7. Cấu Trúc Project

```
project_root/
├── CMakeLists.txt
├── prj.conf
├── boards/
│   └── custom_board/
│       ├── board.h
│       ├── board.c
│       └── Kconfig.board
├── src/
│   ├── main.c
│   ├── drivers/
│   │   ├── lcd_driver.c
│   │   ├── lte_driver.c
│   │   ├── wifi_driver.c
│   │   └── ...
│   ├── ui/
│   │   ├── ui_main.c
│   │   └── screens/
│   ├── network/
│   │   ├── lte_manager.c
│   │   ├── wifi_manager.c
│   │   └── ...
│   ├── io/
│   │   ├── adc_manager.c
│   │   ├── dac_manager.c
│   │   ├── relay_manager.c
│   │   └── ...
│   └── protocols/
│       ├── can_protocol.c
│       └── modbus_protocol.c
├── include/
│   └── ...
└── README.md
```

---

## 8. Testing Strategy

### 8.1. Unit Testing
- Test từng driver độc lập
- Mock hardware interfaces

### 8.2. Integration Testing
- Test các module tích hợp với nhau
- Network connectivity tests
- I/O functionality tests

### 8.3. System Testing
- End-to-end scenarios
- Stress testing
- Long-term stability testing

---

## 9. Documentation Requirements

- [ ] API documentation
- [ ] Driver documentation
- [ ] User manual
- [ ] Configuration guide
- [ ] Troubleshooting guide

---

## 10. Risks & Mitigation

### 10.1. Technical Risks
- **LCD Driver Complexity**: Cần thời gian để tối ưu performance
  - *Mitigation*: Sử dụng LVGL, tận dụng hardware acceleration
  
- **4G Module Integration**: AT command parsing có thể phức tạp
  - *Mitigation*: Sử dụng modem driver có sẵn, test kỹ với module cụ thể
  
- **Memory Constraints**: Frame buffer và data có thể lớn
  - *Mitigation*: Tối ưu memory usage, sử dụng external SDRAM hiệu quả

### 10.2. Schedule Risks
- **Delays in Network Integration**: Có thể mất nhiều thời gian
  - *Mitigation*: Ưu tiên phát triển song song, parallel development

---

## 11. Success Criteria

- [ ] LCD 7 inch hiển thị đúng, mượt mà
- [ ] 4G LTE kết nối ổn định, data transfer hoạt động
- [ ] WiFi kết nối và hoạt động ổn định
- [ ] Tất cả I/O peripherals hoạt động đúng
- [ ] System ổn định, không crash
- [ ] Performance đáp ứng yêu cầu
- [ ] Documentation đầy đủ

---

## 12. References

- Zephyr OS Documentation: https://docs.zephyrproject.org/
- STM32H7 Reference Manual
- LVGL Documentation: https://docs.lvgl.io/
- Modem AT Commands Reference
- WiFi Module Datasheet

---

**Version**: 1.0  
**Last Updated**: 2025  
**Status**: Planning Phase

