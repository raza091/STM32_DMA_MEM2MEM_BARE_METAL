# STM32 DMA Memory-to-Memory Transfer (Bare-Metal)

Copies a buffer between two RAM arrays with the DMA1 controller on an STM32F103RB, using only CMSIS register access (no HAL). The transfer-complete interrupt raises a flag, and the result is printed over USART2.

![MCU](https://img.shields.io/badge/MCU-STM32F103RB-03234B)
![Language](https://img.shields.io/badge/Language-C-blue)
![Style](https://img.shields.io/badge/Style-bare--metal%20CMSIS-lightgrey)

## How it works

1. `main()` prints the source and destination addresses.
2. `dma1_mem2mem_config()` sets up DMA1 Channel 1 in memory-to-memory mode and enables its interrupt in the NVIC at priority 0.
3. `dma_transfer_start(src, dst, len)` loads the addresses and transfer count and enables the channel.
4. The DMA transfer-complete interrupt sets `g_transfer_complete`.
5. `main()` waits on the flag, then prints the destination buffer.

```
sensor_data_arr[5] ──► DMA1 Ch1 (mem2mem) ──► temp_data_arr[5]
                              │
                              └─► TC interrupt ──► g_transfer_complete = 1
```

## Source layout

```
Src/main.c        demo: copy five uint16_t samples and print them
Src/dma.c         DMA1 Channel 1 configuration, start and IRQ handler
Src/uart.c        USART2 TX driver (PA2)
Src/syscalls.c    newlib stubs so printf() writes to the UART
Startup/          startup_stm32f103rbtx.s
STM32F103RBTX_FLASH.ld   linker script
chip_headers/     CMSIS core and STM32F1xx device headers (ARM / STMicroelectronics)
```

## Build and run

1. Open the folder as an existing project in STM32CubeIDE, or build with `arm-none-eabi-gcc` using the linker script and startup file above.
2. Flash an STM32F103RB board and open a serial terminal on USART2 TX (PA2).

## Notes

- `CLK` in `uart.c` assumes a 36 MHz APB1 clock. Adjust it to match your clock configuration.
- PA5 is configured as an output for debug use.

## Related

- [STM32_ADC_TIM_DMA_BARE_METAL](https://github.com/raza091/STM32_ADC_TIM_DMA_BARE_METAL): two-channel ADC streamed to RAM by DMA.

## Author

Ali Raza Tariq. Embedded systems, PCB design and digital IC design. [LinkedIn](https://www.linkedin.com/in/ali-raza-embedded-fpga) · [GitHub](https://github.com/raza091)
