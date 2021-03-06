MMCU=attiny85
F_CPU=16500000
PROG=dragon_isp
PROG_PORT=usb

TARGET=main.hex

OBJ=lm75a.o temperature.o i2cusb.o usbdrv/usbdrv.o usbdrv/usbdrvasm.o usbdrv/oddebug.o Light_WS2812/light_ws2812.o

FORMAT=ihex
OBJCOPY=avr-objcopy
OBJSIZE=avr-size
CC=avr-gcc
CFLAGS=-mmcu=$(MMCU) -I. -ILight_WS2812 -gdwarf-2 -DF_CPU=$(F_CPU)UL -Os -funsigned-char -funsigned-bitfields -fpack-struct -fshort-enums -Wall -Wextra -pedantic -Wundef -std=gnu11 -Wundef -lm
CFLAGS+=-ffunction-sections -fdata-sections -mstrict-X -maccumulate-args -Wl,--gc-sections

all: $(TARGET)

clean:
	rm -f $(TARGET)

usbflash: $(TARGET)
	micronucleus --run --type intel-hex $(TARGET)

flash: $(TARGET)
	sudo avrdude -U flash:w:$(TARGET):i -C /etc/avrdude.conf -v -p $(MMCU) -b 57600 -c $(PROG) -P $(PROG_PORT)

%.hex: %.elf
	$(OBJCOPY) -O $(FORMAT) -R .eeprom $< $@

%.elf: %.o $(OBJ)
	$(CC) $(CFLAGS) $^ --output $@
	$(OBJSIZE) --format=avr --mcu=$(MMCU) $@

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

%.o: %.S
	$(CC) $(CFLAGS) -x assembler-with-cpp -c $< -o $@

%.s: %.c
	$(CC) $(CFLAGS) -S -c $< -o $@