BOARD ?= KIT_XMC47_RELAX_V1
BOARD_DIR = boards/$(BOARD)

ifeq ($(wildcard $(BOARD_DIR)/.),)
  $(error Invalid BOARD specified)
endif

include ../../py/mkenv.mk

CROSS = 0

# qstr definitions (must come before including py.mk)
QSTR_DEFS = qstrdefsport.h

# MicroPython feature configurations
MICROPY_ROM_TEXT_COMPRESSION ?= 1

# include py core make definitions
include ../../py/mkenv.mk
include $(TOP)/py/py.mk

# we are only cross compiling since target is always XMC
CROSS_COMPILE ?= ~/personal_work/binaries/gcc-arm-11.2-2022.02-x86_64-arm-none-eabi/bin/arm-none-eabi-

INC += -I.
INC += -I$(TOP)
INC += -I$(BUILD)
INC += $(BOARD_INC)

LD = ~/personal_work/binaries/gcc-arm-11.2-2022.02-x86_64-arm-none-eabi/bin/arm-none-eabi-gcc
CFLAGS_CORTEX_M4 = -mthumb -mtune=cortex-m4 -mcpu=cortex-m4 -msoft-float -fsingle-precision-constant -Wdouble-promotion -Wfloat-conversion -UMICROPY_USE_INTERNAL_PRINTF

CFLAGS += $(INC) -Wall -Werror -Wdouble-promotion -Wfloat-conversion -std=c99 $(CFLAGS_CORTEX_M4) $(COPT)
CFLAGS += $(BOARD_CFLAGS)

LDFLAGS += -Wl,--cref -Wl,--gc-sections

CSUPEROPT = -Os # save some code space

# Tune for Debugging or Optimization
#CFLAGS += -g  # always include debug info in the ELF
ifeq ($(DEBUG), 1)
CFLAGS += -O0
else
CFLAGS += -Os -DNDEBUG
CFLAGS += -fdata-sections -ffunction-sections
endif

# Flags for optional C++ source code
CXXFLAGS += $(filter-out -std=c99,$(CFLAGS))

LIBS =

SRC_C = \
	main.c \
	uart_core.c \
	\
	shared/readline/readline.c \
	shared/runtime/pyexec.c \
	shared/runtime/stdout_helpers.c
#	
#	$(BUILD)/_frozen_mpy.c 

SRC_C += shared/libc/string0.c

SRC_QSTR += shared/readline/readline.c shared/runtime/pyexec.c

OBJ += $(PY_CORE_O)
OBJ += $(addprefix $(BUILD)/, $(SRC_C:.c=.o))
OBJ += $(addprefix $(BUILD)/, $(SRC_CXX:.cpp=.o))
OBJ += $(BOARD_OBJS)


all: $(BUILD)/firmware.hex

$(BUILD)/_frozen_mpy.c: $(TOP)/tests/frozen/frozentest.mpy $(BUILD)/genhdr/qstrdefs.generated.h
	$(ECHO) "MISC freezing bytecode"
	$(Q)$(TOP)/tools/mpy-tool.py -f -q $(BUILD)/genhdr/qstrdefs.preprocessed.h -mlongint-impl=none $< > $@

$(BUILD)/firmware.elf: $(OBJ)
	$(ECHO) "LINK $@"
	$(Q)$(LD) $(LDFLAGS) -o $@ $^
	$(Q)$(SIZE) $@

$(BUILD)/firmware.hex: $(BUILD)/firmware.elf
	$(Q)$(OBJCOPY) -O ihex $^ $@

# include adapter makefile
include $(BOARD_DIR)/Makefile

include $(TOP)/py/mkrules.mk
