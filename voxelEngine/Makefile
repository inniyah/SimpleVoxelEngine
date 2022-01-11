#!/usr/bin/make -f

LIB_NAME=libvoxelengine

APP_NAMES=

NUMCPUS=$(shell grep -c '^processor' /proc/cpuinfo)

MCU_FLAGS = 

TRGT = 
CC   = $(TRGT)gcc
CXX  = $(TRGT)g++

ARCH_NAME:=$(shell '$(CC)' -dumpmachine)

CURDIR:=$(shell dirname $(realpath $(firstword $(MAKEFILE_LIST))))
OUT_DIR:=$(CURDIR)/$(ARCH_NAME)/out
OBJ_DIR:=$(CURDIR)/$(ARCH_NAME)/obj
CFG_DIR:=$(CURDIR)/$(ARCH_NAME)/config
GLAD_DIR:=$(CURDIR)/glad

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

GLAD = glad

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
	$(PKG_CONFIG_LIBS)

CSTD=-std=gnu17
CPPSTD=-std=gnu++17

DEFS= \
	-DDEBUG

INCS= \
	-Isrc \
	-Iinclude \
	-I$(GLAD_DIR)/include

OBJS= \
	$(OBJ_DIR)/AABBTree.o \
	$(OBJ_DIR)/BehaviourFactory.o \
	$(OBJ_DIR)/behaviours/VoxelatedConstructionSpriteBehaviour.o \
	$(OBJ_DIR)/Camera.o \
	$(OBJ_DIR)/Chunk.o \
	$(OBJ_DIR)/ChunkManager.o \
	$(OBJ_DIR)/Controls.o \
	$(OBJ_DIR)/ISprite.o \
	$(OBJ_DIR)/IVoxel.o \
	$(OBJ_DIR)/MouseAndKeyboardCameraControllerInput.o \
	$(OBJ_DIR)/ShaderManager.o \
	$(OBJ_DIR)/SpriteManager.o \
	$(OBJ_DIR)/UniformChunk.o \
	$(OBJ_DIR)/VoxelContainerGeometry.o \
	$(OBJ_DIR)/VoxelEngine.o \
	$(OBJ_DIR)/VoxelGeometry.o \
	$(OBJ_DIR)/VoxelRenderer.o \
	$(OBJ_DIR)/VoxelSprite.o \
	$(OBJ_DIR)/glad.o

all: $(GLAD_DIR)/src/glad.c $(OUT_DIR)/$(LIB_NAME).a $(APP_NAMES:%=$(OUT_DIR)/%)


$(OUT_DIR)/%: $(OBJ_DIR)/%.o $(OBJS)
	@mkdir -p "$$(dirname '$@')"
	$(LD) $(CPPSTD) $(CSTD) $(MCU_FLAGS) $(OPTS) $(LD_OPTS) $(LDFLAGS) -o $@ $+ $(LIBS)

$(OUT_DIR)/$(LIB_NAME).a: $(OBJS)
	$(AR) $(LIB_NAME).a $(OBJS)


$(OBJ_DIR)/%.o: src/%.s
	@mkdir -p "$$(dirname '$@')"
	$(AS) $(CSTD) $(MCU_FLAGS) $(OPTS) $(ASM_OPTS) -o $@ -c $< $(DEFS) $(INCS) $(CFLAGS) $(CWARN)

$(OBJ_DIR)/%.o: src/%.c
	@mkdir -p "$$(dirname '$@')"
	$(CC) $(CSTD) $(MCU_FLAGS) $(OPTS) $(C_OPTS) -o $@ -c $< $(DEFS) $(INCS) $(CFLAGS) $(CWARN)

$(OBJ_DIR)/%.o: src/%.cpp
	@mkdir -p "$$(dirname '$@')"
	$(CXX) $(CPPSTD) $(MCU_FLAGS) $(OPTS) $(CXX_OPTS) -o $@ -c $< $(DEFS) $(INCS) $(CFLAGS) $(CPPWARN)

$(OBJ_DIR)/glad.o: $(GLAD_DIR)/src/glad.c
	@mkdir -p "$$(dirname '$@')"
	$(CC) $(CSTD) $(MCU_FLAGS) $(OPTS) $(C_OPTS) -o $@ -c $< $(DEFS) $(INCS) $(CFLAGS) $(CWARN)

$(GLAD_DIR)/src/glad.c $(GLAD_DIR)/include/glad/glad.h:
	$(GLAD) --generator c --spec gl --out-path $(GLAD_DIR)

clean:
	$(RM) $(OBJS) $(APP_NAMES)
	$(RM) --recursive  $(OUT_DIR)/ $(OBJ_DIR)/ $(CFG_DIR)/ $(CURDIR)/$(ARCH_NAME)/ $(GLAD_DIR)/
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