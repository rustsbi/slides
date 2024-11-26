# Research on Bouffalo Chip TF Card Image Format

## Background
We are designing a bootloader product for the Bouffalo chip in its initial phase. This bootloader will include basic functionalities such as initializing PSRAM memory and peripherals like UART, scanning the TF card to load and start the operating system, and providing command line operations and debugging capabilities. To ensure the bootloader's universality and compatibility, we need to choose a standard TF card image format.

## Research

### Bouffalo

We couldn't find any official documentation from Bouffalo regarding the TF card image format in their bootloader. However, from the [bouffalo_sdk](https://github.com/bouffalolab/bouffalo_sdk), we discovered that the examples related to TF cards both utilize FAT32 formatted partitions:

![image](https://github.com/user-attachments/assets/331370b1-a2b2-4dee-8279-b7f4045cff66)


Regarding the partition table format, the code does not specify a preference. Both MBR and GPT partition table formats are supported.

![image](https://github.com/user-attachments/assets/495cda4d-e3dd-4573-895c-cd4566febe80)


### M1s Dock

In the [documentation](https://wiki.sipeed.com/hardware/zh/maix/m1s/other/start.html) for the M1s Dock development board, we found that its firmware provides a virtual USB drive for users. This virtual USB drive uses an MBR partition table format with FAT16 partitions.

![image](https://github.com/user-attachments/assets/6b730d4f-8619-4b38-8afd-9e3fca365998)

![image](https://github.com/user-attachments/assets/1eca685b-f9c2-4e68-80bc-d0f0f976d78f)


## Conclusion

Based on our research on the Bouffalo chip and the M1s Dock development board, we can draw the following conclusions:

- TF Card Image Format:
  - The bootloader examples for the Bouffalo chip use FAT32 formatted partitions, indicating that this format is widely supported within the Bouffalo ecosystem.
  - The M1s Dock development board uses FAT16 formatted partitions, likely due to its smaller storage capacity.

- Partition Table Format:
  - Bouffalo's code supports both MBR and GPT partition table formats, providing flexibility for various application scenarios.
  - The M1s Dock firmware employs an MBR partition table format, which is a common choice in embedded systems.

In summary, we recommend implementing our bootloader primarily on an MBR partition table with FAT32 formatted partitions, while considering compatibility with GPT and other formats in the future.
