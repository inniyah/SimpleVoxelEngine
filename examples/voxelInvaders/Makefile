#!/usr/bin/make -f

APP_NAME=voxelInvaders

NUMCPUS=$(shell grep -c '^processor' /proc/cpuinfo)

MCU_FLAGS = 

TRGT = 
CC   = $(TRGT)gcc
CXX  = $(TRGT)g++

ARCH_NAME:=$(shell '$(CC)' -dumpmachine)

CURDIR:=$(shell dirname $(realpath $(firstword $(MAKEFILE_LIST))))
LIB_DIR:=$(CURDIR)/../../voxelEngine
OUT_DIR:=$(CURDIR)/$(ARCH_NAME)/out
OBJ_DIR:=$(CURDIR)/$(ARCH_NAME)/obj
CFG_DIR:=$(CURDIR)/$(ARCH_NAME)/config
GLAD_DIR:=$(LIB_DIR)/glad

LD   = $(TRGT)g++
AR   = $(TRGT)ar rvc

CP   = $(TRGT)objcopy
AS   = $(TRGT)gcc -x assembler-with-cpp
OD   = $(TRGT)objdump
SZ   = $(TRGT)size
HEX  = $(CP) -O ihex
MOT  = $(CP) -O srec
BIN  = $(CP) -O binary

RM   = rm --force --verbose

# Define C warning options here
CWARN = -Wall -Wextra -Wstrict-prototypes -Wno-unused-parameter

# Define C++ warning options here.
CPPWARN = -Wall -Wextra -Wno-unused-parameter

# Compiler options here.
OPTS = \
	-O2 -gdwarf-2 \
	-fomit-frame-pointer \
	-fdiagnostics-color=always

# ASM specific options here.
ASM_OPTS = 

# C specific options here.
C_OPTS = 

# C++ specific options here.
CXX_OPTS = 

PKG_CONFIG= glm gl glfw3

ifndef PKG_CONFIG
PKG_CONFIG_CFLAGS=
PKG_CONFIG_LDFLAGS=
PKG_CONFIG_LIBS=
else
PKG_CONFIG_CFLAGS=`pkg-config --cflags $(PKG_CONFIG)`
PKG_CONFIG_LDFLAGS=`pkg-config --libs-only-L $(PKG_CONFIG)`
PKG_CONFIG_LIBS=`pkg-config --libs-only-l $(PKG_CONFIG)`
endif

LD_OPTS = \
	-ffunction-sections \
	-fdata-sections \
	-fno-common \
	-Wl,--cref,--no-warn-mismatch,--library-path=,--gc-sections \
	-Wl,-Map="$(OUT_DIR)/$(APP_NAME).map"

CFLAGS= \
	$(PKG_CONFIG_CFLAGS) \
	-fwrapv \
	-fstack-protector-strong \
	-fdata-sections \
	-ffunction-sections \
	-fno-strict-aliasing \
	-Wformat \
	-Werror=format-security \
	-Wdate-time \
	-D_FORTIFY_SOURCE=2 \
	-no-pie

LDFLAGS= \
	$(PKG_CONFIG_LDFLAGS) \
	-Wl,-O1 \
	-Wl,-Bsymbolic-functions \
	-Wl,-z,relro \
	-Wl,--as-needed \
	-Wl,--no-undefined \
	-Wl,--no-allow-shlib-undefined \
	-Wl,-Bsymbolic-functions \
	-Wl,--dynamic-list-cpp-new \
	-Wl,--dynamic-list-cpp-typeinfo \
	-no-pie

LIBS= \
	$(PKG_CONFIG_LIBS) \
	-L "$(LIB_DIR)" \
	-lvoxelengine \
	-lpthread \
	-ldl

CSTD=-std=gnu17
CPPSTD=-std=gnu++17

DEFS= \
	-DDEBUG

INCS= \
	-I$(LIB_DIR)/include \
	-I$(GLAD_DIR)/include

OBJS= \
	$(OBJ_DIR)/BulletMovementSpriteBehaviour.o \
	$(OBJ_DIR)/InvaderMovementSpriteBehaviour.o \
	$(OBJ_DIR)/PlayerMovementBehaviour.o \
	$(OBJ_DIR)/TerrainChunkFactory.o \
	$(OBJ_DIR)/voxelInvaders.o

all: $(APP_NAME)

$(APP_NAME): $(OBJS)
	@mkdir -p "$(OUT_DIR)"
	@mkdir -p "$$(dirname '$@')"
	$(LD) $(CPPSTD) $(CSTD) $(MCU_FLAGS) $(OPTS) $(LD_OPTS) $(LDFLAGS) -o $@ $+ $(LIBS)

$(OBJ_DIR)/%.o: ./%.s
	@mkdir -p "$$(dirname '$@')"
	$(AS) $(CSTD) $(MCU_FLAGS) $(OPTS) $(ASM_OPTS) -o $@ -c $< $(DEFS) $(INCS) $(CFLAGS) $(CWARN)

$(OBJ_DIR)/%.o: ./%.c
	@mkdir -p "$$(dirname '$@')"
	$(CC) $(CSTD) $(MCU_FLAGS) $(OPTS) $(C_OPTS) -o $@ -c $< $(DEFS) $(INCS) $(CFLAGS) $(CWARN)

$(OBJ_DIR)/%.o: ./%.cpp
	@mkdir -p "$$(dirname '$@')"
	$(CXX) $(CPPSTD) $(MCU_FLAGS) $(OPTS) $(CXX_OPTS) -o $@ -c $< $(DEFS) $(INCS) $(CFLAGS) $(CPPWARN)

clean:
	$(RM) $(OBJS) $(APP_NAME)
	$(RM) --recursive  $(OUT_DIR)/ $(OBJ_DIR)/ $(CFG_DIR)/ $(CURDIR)/$(ARCH_NAME)/
	@find . -name '*.o' -exec $(RM) {} +
	@find . -name '*.a' -exec $(RM) {} +
	@find . -name '*.so' -exec $(RM) {} +
	@find . -name '*.map' -exec $(RM) {} +
	@find . -name '*.elf' -exec $(RM) {} +
	@find . -name '*.hex' -exec $(RM) {} +
	@find . -name '*.bin' -exec $(RM) {} +
	@find . -name '*.dmp' -exec $(RM) {} +
	@find . -name '*.pyc' -exec $(RM) {} +
	@find . -name '*.pyo' -exec $(RM) {} +
	@find . -name '*.bak' -exec $(RM) {} +
	@find . -name '*~' -exec $(RM) {} +
	@$(RM) core

.PHONY: all clean