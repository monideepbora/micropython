# Board-level makefile

# Board-specific variables
XMC_VARIANT = XMC4700
XMC_PART = XMC4700x2048



BOARD_OBJS = $(addsuffix .o,$(basename $(wildcard *.S *.c)))

BOARD_CFLAGS = -mcpu=cortex-m4 -mthumb -fpermissive -c -g -Os -w -std=gnu++11 -ffunction-sections -fdata-sections -fno-threadsafe-statics -nostdlib --param max-inline-insns-single=500 -fno-rtti -fno-exceptions -w -x c++ -E -CC -DXMC4700_F144x2048 -DXMC4700_Relax_Kit -DF_CPU=144000000L -DARDUINO=4700 -DARDUINO_ARM_XMC -DARDUINO_ARCH_XMC -DARM_MATH_CM4 -DXMC4_SERIES -DARM_LIB_CMSIS_DSP -DARM_LIB_CMSIS_NN 

BOARD_LDFLAGS = -mthumb --specs=nosys.specs --specs=nano.specs -mcpu=cortex-m4 -Os -Wl,--gc-sections -save-temps -T$(XMC_PART).ld -Wl,-Map, $(XMC_PART).map