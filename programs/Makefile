# ##########################################################################
# LZ4 programs - Makefile
# Copyright (C) Yann Collet 2011-2016
#
# GPL v2 License
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License along
# with this program; if not, write to the Free Software Foundation, Inc.,
# 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.
#
# You can contact the author at :
#  - LZ4 homepage : http://www.lz4.org
#  - LZ4 source repository : https://github.com/Cyan4973/lz4
# ##########################################################################
# lz4 : Command Line Utility, supporting gzip-like arguments
# lz4c  : CLU, supporting also legacy lz4demo arguments
# lz4c32: Same as lz4c, but forced to compile in 32-bits mode
# ##########################################################################

# Version numbers
LIBVER_SRC := ../lib/lz4.h
LIBVER_MAJOR_SCRIPT:=`sed -n '/define LZ4_VERSION_MAJOR/s/.*[[:blank:]]\([0-9][0-9]*\).*/\1/p' < $(LIBVER_SRC)`
LIBVER_MINOR_SCRIPT:=`sed -n '/define LZ4_VERSION_MINOR/s/.*[[:blank:]]\([0-9][0-9]*\).*/\1/p' < $(LIBVER_SRC)`
LIBVER_PATCH_SCRIPT:=`sed -n '/define LZ4_VERSION_RELEASE/s/.*[[:blank:]]\([0-9][0-9]*\).*/\1/p' < $(LIBVER_SRC)`
LIBVER_SCRIPT:= $(LIBVER_MAJOR_SCRIPT).$(LIBVER_MINOR_SCRIPT).$(LIBVER_PATCH_SCRIPT)
LIBVER_MAJOR := $(shell echo $(LIBVER_MAJOR_SCRIPT))
LIBVER_MINOR := $(shell echo $(LIBVER_MINOR_SCRIPT))
LIBVER_PATCH := $(shell echo $(LIBVER_PATCH_SCRIPT))
LIBVER  := $(shell echo $(LIBVER_SCRIPT))

DESTDIR ?=
PREFIX  ?= /usr/local
BINDIR  := $(PREFIX)/bin
MANDIR  := $(PREFIX)/share/man/man1
LZ4DIR  := ../lib
VOID    := /dev/null

CPPFLAGS+= -I$(LZ4DIR) -DXXH_NAMESPACE=LZ4_
CFLAGS  ?= -O3
DEBUGFLAGS:=-g -Wall -Wextra -Wundef -Wcast-qual -Wcast-align -Wshadow \
           -Wswitch-enum -Wdeclaration-after-statement -Wstrict-prototypes \
           -Wpointer-arith -Wstrict-aliasing=1
CFLAGS  += $(MOREFLAGS)
FLAGS    = $(CFLAGS) $(CPPFLAGS) $(LDFLAGS)

LZ4_VERSION=$(LIBVER)
MD2ROFF  =ronn
MD2ROFF_FLAGS  = --roff --warnings --manual="User Commands" --organization="lz4 $(LZ4_VERSION)"


# Define *.exe as extension for Windows systems
ifneq (,$(filter Windows%,$(OS)))
EXT =.exe
else
EXT =
endif



default: lz4-release

lz4-release: $(LZ4DIR)/lz4.o $(LZ4DIR)/lz4hc.o $(LZ4DIR)/lz4frame.o $(LZ4DIR)/xxhash.o bench.o lz4io.o lz4cli.o datagen.o
	$(CC) $(FLAGS) $^ -o lz4$(EXT)

all: lz4 lz4c

all32: CFLAGS+=-m32
all32: all

lz4: CFLAGS += $(DEBUGFLAGS)
lz4: lz4-release

lz4c: CFLAGS += $(DEBUGFLAGS)
lz4c  : $(LZ4DIR)/lz4.o $(LZ4DIR)/lz4hc.o $(LZ4DIR)/lz4frame.o $(LZ4DIR)/xxhash.o bench.o lz4io.o lz4cli.c datagen.o
	$(CC) $(FLAGS) -DENABLE_LZ4C_LEGACY_OPTIONS $^ -o $@$(EXT)

lz4c32: CFLAGS+=-m32
lz4c32: lz4
	@cp lz4$(EXT) lz4c32$(EXT)

clean:
	@$(MAKE) -C $(LZ4DIR) $@ > $(VOID)
	@$(RM) core *.o *.test tmp* \
           lz4$(EXT) lz4c$(EXT) lz4c32$(EXT) unlz4 lz4cat
	@echo Cleaning completed


lz4.1: lz4.1.md
	cat $^ | $(MD2ROFF) $(MD2ROFF_FLAGS) | sed -n '/^\.\\\".*/!p' > $@

man: lz4.1

clean-man:
	rm lz4.1

preview-man: clean-man man
	man ./lz4.1


#------------------------------------------------------------------------
#make install is validated only for Linux, OSX, kFreeBSD, Hurd and
#FreeBSD targets
ifneq (,$(filter $(shell uname),Linux Darwin GNU/kFreeBSD GNU FreeBSD))

unlz4: lz4
	ln -s lz4 unlz4

lz4cat: lz4
	ln -s lz4 lz4cat

install: lz4$(EXT) lz4c$(EXT)
	@echo Installing binaries
	@install -d -m 755 $(DESTDIR)$(BINDIR)/ $(DESTDIR)$(MANDIR)/
	@install -m 755 lz4 $(DESTDIR)$(BINDIR)/lz4
	@ln -sf lz4 $(DESTDIR)$(BINDIR)/lz4cat
	@ln -sf lz4 $(DESTDIR)$(BINDIR)/unlz4
	@install -m 755 lz4c$(EXT) $(DESTDIR)$(BINDIR)/lz4c
	@echo Installing man pages
	@install -m 644 lz4.1 $(DESTDIR)$(MANDIR)/lz4.1
	@ln -sf lz4.1 $(DESTDIR)$(MANDIR)/lz4c.1
	@ln -sf lz4.1 $(DESTDIR)$(MANDIR)/lz4cat.1
	@ln -sf lz4.1 $(DESTDIR)$(MANDIR)/unlz4.1
	@echo lz4 installation completed

uninstall:
	@$(RM) $(DESTDIR)$(BINDIR)/lz4cat
	@$(RM) $(DESTDIR)$(BINDIR)/unlz4
	@$(RM) $(DESTDIR)$(BINDIR)/lz4
	@$(RM) $(DESTDIR)$(BINDIR)/lz4c
	@$(RM) $(DESTDIR)$(MANDIR)/lz4.1
	@$(RM) $(DESTDIR)$(MANDIR)/lz4c.1
	@$(RM) $(DESTDIR)$(MANDIR)/lz4cat.1
	@$(RM) $(DESTDIR)$(MANDIR)/unlz4.1
	@echo lz4 programs successfully uninstalled

endif
