ARMGNU ?= aarch64-linux-gnu

CFLAGS = -Wall -nostdlib -nostartfiles -ffreestanding -Iinclude -mgeneral-regs-only -g
ASMFLAGS = -Iinclude
QEMUFLAGS = -M raspi3b -display none -serial null -serial stdio

SRC_DIR = src
BUILD_DIR = build

all: kernel8.img

clean:
	rm -rf $(BUILD_DIR) *.img

$(BUILD_DIR)/%.o: $(SRC_DIR)/%.c
	mkdir -p $(@D)
	$(ARMGNU)-gcc $(CFLAGS)   -c $< -o $@

$(BUILD_DIR)/%.o: %.S
	$(ARMGNU)-gcc $(ASMFLAGS) -c $< -o $@

SRC_FILES  = $(wildcard $(SRC_DIR)/*.c)
ASM_FILES  = $(wildcard *.S)
OBJ_FILES  = $(SRC_FILES:$(SRC_DIR)/%.c=$(BUILD_DIR)/%.o)
OBJ_FILES += $(ASM_FILES:%.S=$(BUILD_DIR)/%.o)

kernel8.img: linker.ld $(OBJ_FILES)
	$(ARMGNU)-ld -T linker.ld -o $(BUILD_DIR)/kernel8.elf $(OBJ_FILES)
	$(ARMGNU)-objcopy -O binary  $(BUILD_DIR)/kernel8.elf kernel8.img

qemu: kernel8.img
	qemu-system-aarch64 $(QEMUFLAGS) -kernel $<

debug: kernel8.img
	qemu-system-aarch64 $(QEMUFLAGS) -kernel $< -S -s