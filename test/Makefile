# This Makefile assumes the top folder has been built
TOP = ..
CC ?= gcc

COGUTILS_DIR  := $(TOP)/cog-utils
CORE_DIR      := $(TOP)/core
INCLUDE_DIR   := $(TOP)/include
GENCODECS_DIR := $(TOP)/gencodecs

TEST_DISCORD := rest sync async timeout
TEST_CORE    := user-agent websockets

EXES := $(TEST_DISCORD) $(TEST_GITHUB) $(TEST_CORE)

CFLAGS  = -I$(INCLUDE_DIR) -I$(COGUTILS_DIR) -I$(CORE_DIR) \
          -I$(CORE_DIR)/third-party -I$(GENCODECS_DIR)     \
          -O0 -g -pthread -Wall
LDFLAGS = -ldiscord -L$(TOP)/lib -lcurl

all: $(EXES)

.SUFFIXES:
.DEFAULT:
	$(CC) $(CFLAGS) -o $@ $@.c $(LDFLAGS)

echo:
	@ echo -e 'CC: $(CC)\n'
	@ echo -e 'EXES: $(EXES)\n'

clean:
	@ $(RM) $(EXES)

.PHONY: all echo clean
