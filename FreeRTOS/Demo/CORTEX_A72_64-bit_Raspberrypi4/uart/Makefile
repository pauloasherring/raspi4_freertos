# To keep compatibility with orignal work, check if CROSS is defined
# and if so, override CC, OBJDUMP, AS and OBJCOPY, which are used
# by yocto by default.
# Had to remove the default weak asignment for CROSS, otherwise, this
# would not work.
ifdef CROSS
	CC=$(CROSS)gcc
	OBJDUMP=$(CROSS)objdump
	AS=$(CROSS)as
	OBJCOPY=$(CROSS)objcopy
endif

CFLAGS = -mcpu=cortex-a72 \
         -fpic \
         -ffreestanding \
         -std=gnu99 \
         -O2 \
         -Wall \
         -Wextra \
         -DGUEST \
         -I$(INCLUDEPATH1) \
         -I$(INCLUDEPATH2) \
         -I$(INCLUDEPATH3) \
         -I$(INCLUDEPATH4) \
         -I$(INCLUDEPATH5)
BUILTIN_OPS = -fno-builtin-memset
ASMFLAGS = -mcpu=cortex-a72

INCLUDEPATH1 ?= ./src
INCLUDEPATH2 ?= ../musl_libc
INCLUDEPATH3 ?= ../mmu
INCLUDEPATH4 ?= ../../../Source/include
INCLUDEPATH5 ?= ../../../Source/portable/GCC/ARM_CA72_64_BIT

# From ./src
OBJS = build/startup.o 
OBJS +=build/FreeRTOS_asm_vector.o
OBJS +=build/FreeRTOS_tick_config.o
OBJS +=build/interrupt.o
OBJS +=build/main.o
OBJS +=build/mmu_cfg.o
OBJS +=build/uart.o

# From ../mmu
OBJS +=build/mmu.o

# From ../cache
OBJS +=build/cache.o

# From ../musl_libc
OBJS +=build/memset.o
OBJS +=build/memcpy.o

# From ../../../Source/portable/GCC/ARM_CA72_64_BIT
OBJS +=build/port.o
OBJS +=build/portASM.o

OBJS +=build/list.o
OBJS +=build/queue.o
OBJS +=build/tasks.o
OBJS +=build/timers.o

OBJS +=build/heap_1.o

uart.elf : src/raspberrypi4.ld $(OBJS)
	$(CC) -Wl,--build-id=none -std=gnu99 -T src/raspberrypi4.ld -o $@ -ffreestanding -nostdlib --specs=nosys.specs $(BUILTIN_OPS) $(OBJS)
	$(OBJDUMP) -d uart.elf > uart.list
	$(OBJCOPY) -O binary uart.elf uart.bin

build/%.o : src/%.S
	$(AS) $(ASMFLAGS) -c -o $@ $<
	
build/%.o : src/%.c
	$(CC) $(CFLAGS)  -c -o $@ $<

build/%.o : ../mmu/%.c
	$(CC) $(CFLAGS)  -c -o $@ $<

build/%.o : ../cache/%.S
	$(CC) $(CFLAGS)  -c -o $@ $<

build/%.o : ../musl_libc/%.c
	$(CC) $(CFLAGS)  -c -o $@ $<

build/%.o : ../../../Source/%.c
	$(CC) $(CFLAGS)  -c -o $@ $<

build/%.o : ../../../Source/portable/GCC/ARM_CA72_64_BIT/%.c
	$(CC) $(CFLAGS)  -c -o $@ $<

build/%.o : ../../../Source/portable/GCC/ARM_CA72_64_BIT/%.S
	$(AS) $(ASMFLAGS) -c -o $@ $<

build/%.o : ../../../Source/portable/MemMang/%.c
	$(CC) $(CFLAGS)  -c -o $@ $<

clean :
	rm -f build/*.o
	rm -f *.elf
	rm -f *.list
	rm -f *.bin

