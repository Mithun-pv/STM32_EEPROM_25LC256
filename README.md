# STM32 SPI EEPROM Interface — 25LC256

## Overview
Write and read byte data to Microchip 25LC256 SPI EEPROM  
using STM32F446RET6 with HAL library. Includes WIP bit polling  
and UART debug output to verify read/write operations.

---

## Hardware
| Component | Detail |
|-----------|--------|
| MCU | STM32F446RET6 |
| EEPROM | Microchip 25LC256 (32KB, SPI) |
| CS Pin | PC7 (GPIOC, GPIO_PIN_7) |
| Debug Output | USART2 → 115200 baud |

---

## Pin Connections
| 25LC256 Pin | Pin Name | STM32 Pin |
|-------------|----------|-----------|
| Pin 1 | CS | PC7 |
| Pin 2 | MISO | PA6 (SPI1_MISO) |
| Pin 5 | MOSI | PA7 (SPI1_MOSI) |
| Pin 6 | SCK | PA5 (SPI1_SCK) |
| Pin 7 | HOLD | 3.3V (tied HIGH) |
| Pin 8 | VCC | 3.3V |
| Pin 4 | GND | GND |

---

## SPI Configuration
| Parameter | Value |
|-----------|-------|
| Peripheral | SPI1 |
| Mode | Master, Full Duplex |
| Data Size | 8-bit |
| Clock Polarity | Low (CPOL = 0) |
| Clock Phase | 1 Edge (CPHA = 0) |
| SPI Mode | Mode 0 |
| NSS | Software Controlled |
| Baud Rate Prescaler | /2 |
| First Bit | MSB |

---

## Clock Configuration
| Parameter | Value |
|-----------|-------|
| Clock Source | HSI (Internal) |
| PLL | Disabled |
| System Clock | 16 MHz |
| SPI1 Clock | 16 MHz ÷ 2 = **8 MHz** |
| APB1 Divider | /1 |
| APB2 Divider | /1 |

> HSI runs at 16 MHz by default on STM32F446.  
> With BaudRatePrescaler = /2, SPI clock = **8 MHz**,  
> which is within the 25LC256's maximum SPI speed of 10 MHz.

---

## EEPROM Opcode Table (25LC256)
| Opcode | Instruction | Used In |
|--------|-------------|---------|
| 0x06 | WREN — Write Enable | `EEPROM_WriteEnable()` |
| 0x02 | WRITE — Write Data | `EEPROM_WriteByte()` |
| 0x03 | READ — Read Data | `EEPROM_ReadByte()` |
| 0x05 | RDSR — Read Status Register | `EEPROM_ReadStatus()` |

---

## Functions
| Function | Description |
|----------|-------------|
| `EEPROM_Select()` | Pull CS LOW — activates EEPROM |
| `EEPROM_Deselect()` | Pull CS HIGH — deactivates EEPROM |
| `EEPROM_WriteEnable()` | Sends WREN (0x06) before every write |
| `EEPROM_WriteByte(addr, data)` | Writes 1 byte to 16-bit address |
| `EEPROM_ReadByte(addr)` | Reads 1 byte from 16-bit address |
| `EEPROM_ReadStatus()` | Reads WIP bit — waits for write to finish |

---

## Write Cycle — How It Works
```
1. Send WREN (0x06)          → Enable write operation
2. Pull CS LOW               → Select EEPROM
3. Send [0x02, AddrMSB, AddrLSB, Data]
4. Pull CS HIGH              → Latch write command
5. Poll Status Register      → Wait until WIP bit = 0
```

---

## UART Output (USART2 — 115200 baud)
```
STM32 EEPROM SPI Test
Wrote: 0xA5, Read: 0xA5
Wrote: 0xA5, Read: 0xA5
```

---

## Tools Used
- STM32CubeIDE
- STM32 HAL Library
- Serial Monitor at 115200 baud

---

## Status
✅ Write byte — working  
✅ Read byte — working  
✅ WIP polling — working  
✅ UART debug output — working
