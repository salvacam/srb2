#
# SRB2 for the RetroFW
#
# by pingflood; 2019
#

TARGET ?= srb2/srb2.elf


# SRB2 data files
D_DIR ?=../bin/Resources
D_FILES=$(D_DIR)/srb2.srb \
	$(D_DIR)/sonic.plr \
	$(D_DIR)/tails.plr \
	$(D_DIR)/knux.plr \
	$(D_DIR)/rings.wpn \
	$(D_DIR)/drill.dta \
	$(D_DIR)/soar.dta \
	$(D_DIR)/music.dta \

PKG_CONFIG?=pkg-config

GCW0=1

LINUX=1
NOASM=1
NOUPX=1
NOOBJDUMP=1
NONX86=1
NOHS=1
NOMD5=1
NONET=1
NOPNG=1
NOHW=1
SDL=1
NOVERSION=1

#PREFIX=$(CHAINPREFIX)/usr/bin/mipsel-linux
PREFIX=$(CHAINPREFIX)/usr/bin/arm-linux

CC ?= $(CROSS_COMPILE)gcc
LD ?= $(CROSS_COMPILE)gcc
CXX ?= $(CROSS_COMPILE)g++
STRIP ?= $(CROSS_COMPILE)strip

OBJCOPY ?= $(CROSS_COMPILE)objcopy

SYSROOT     ?= $(shell $(CXX) --print-sysroot)
SDL_CFLAGS  ?= $(shell $(SYSROOT)/usr/bin/sdl-config --cflags)
SDL_LIBS    ?= $(shell $(SYSROOT)/usr/bin/sdl-config --libs)

SDL_CONFIG=$(SYSROOT)/usr/bin/sdl-config

# EXENAME ?= srb2.elf

#CFLAGS+= -DGCW0 -DNOASM -O2 -fomit-frame-pointer -ffunction-sections -ffast-math -fsingle-precision-constant \
# -G0 -march=mips32 -mtune=mips32

CFLAGS+= -DGCW0 -DMIYOO_MODE -DNOASM -O2 -fomit-frame-pointer -ffunction-sections -ffast-math -fsingle-precision-constant \
 -march=armv5te -mtune=arm926ej-s
LDFLAGS+= -Wl,--gc-sections

ifdef LINUX64
	LINUX=1
	NONX86=1
endif

include Makefile.cfg

ifdef UNIX
	UNIXCOMMON=1
endif

ifdef LINUX
	UNIXCOMMON=1
endif

include sdl/Makefile.cfg

ifdef NONET
	OPTS+=-DNONET
else
	ifdef NOIPX
		OPTS+=-DNOIPX
	endif
endif

ifdef NOHW
	OPTS+=-DNOHW
else
	OPTS+=-DHWRENDER
	OBJS+=$(OBJDIR)/hw_bsp.o $(OBJDIR)/hw_draw.o $(OBJDIR)/hw_light.o \
		 $(OBJDIR)/hw_main.o $(OBJDIR)/hw_md2.o $(OBJDIR)/hw_cache.o $(OBJDIR)/hw_trick.o
endif

ifdef NOHS
	OPTS+=-DNOHS
else
	OPTS+=-DHW3SOUND
	OBJS+=$(OBJDIR)/hw3sound.o
endif

ifndef NOVERSION
	OPTS += -DCOMPVERSION
endif

ifndef NONX86
	ifndef GCC29
		M5=-march=pentium
		M4=-march=i486
	else
		M5=-mpentium
		M4=-m486
	endif
endif

ifndef NOASM
	ifndef NONX86
		OBJS+=$(OBJDIR)/tmap.o $(OBJDIR)/tmap_mmx.o
		OPTS+=-DUSEASM
	endif
endif

ifndef NOPNG
	OPTS+=-DHAVE_PNG

	ifdef PNG_PKGCONFIG
		PNG_CFLAGS?=$(shell $(PKG_CONFIG) $(PNG_PKGCONFIG) --cflags)
		PNG_LDFLAGS?=$(shell $(PKG_CONFIG) $(PNG_PKGCONFIG) --libs)
	else
		ifdef PREFIX
			PNG_CONFIG?=$(PREFIX)-libpng-config
		else
			PNG_CONFIG?=libpng-config
		endif

		ifdef PNG_STATIC
			PNG_CFLAGS?=$(shell $(PNG_CONFIG) --static --cflags)
			PNG_LDFLAGS?=$(shell $(PNG_CONFIG) --static --ldflags)
		else
			PNG_CFLAGS?=$(shell $(PNG_CONFIG) --cflags)
			PNG_LDFLAGS?=$(shell $(PNG_CONFIG) --ldflags)
		endif
	endif

	LIBS+=$(PNG_LDFLAGS)
	CFLAGS+=$(PNG_CFLAGS)
endif

ifdef STATIC
	LIBS:=-static $(LIBS)
endif

ifdef NOMD5
	OPTS+=-DNOMD5
else
	OBJS:=$(OBJDIR)/md5.o $(OBJS)
endif

ifdef NOPOSTPROCESSING
	OPTS+=-DNOPOSTPROCESSING
endif

OPTS:=-fno-exceptions $(OPTS)

ifdef DEBUGMODE
	# build with debugging information
	WINDRESFLAGS = -D_DEBUG
	CFLAGS+=-O0 -Wall -DPARANOIA -DRANGECHECK
else
	# build a normal optimised version
	WINDRESFLAGS = -DNDEBUG
	CFLAGS+=-O2
endif

CFLAGS+=-g $(OPTS) $(M5) $(WINDRESFLAGS)

NASMOPTS?= -g

ifdef PROFILEMODE
	# build with profiling information
	CFLAGS:=-pg $(CFLAGS)
endif

ifdef ZDEBUG
	CPPFLAGS+=-DZDEBUG
endif

OPTS+=$(CPPFLAGS)

# default EXENAME if all else fails
# EXENAME ?= srb2.elf
DBGNAME ?= $(EXENAME).debug

# not too sophisticated dependency
OBJS := $(i_main_o) \
		$(OBJDIR)/comptime.o \
		$(OBJDIR)/string.o   \
		$(OBJDIR)/d_main.o   \
		$(OBJDIR)/d_clisrv.o \
		$(OBJDIR)/d_net.o    \
		$(OBJDIR)/d_netfil.o \
		$(OBJDIR)/d_netcmd.o \
		$(OBJDIR)/dehacked.o \
		$(OBJDIR)/dstrings.o \
		$(OBJDIR)/z_zone.o   \
		$(OBJDIR)/f_finale.o \
		$(OBJDIR)/f_wipe.o   \
		$(OBJDIR)/g_game.o   \
		$(OBJDIR)/g_input.o  \
		$(OBJDIR)/am_map.o   \
		$(OBJDIR)/command.o  \
		$(OBJDIR)/console.o  \
		$(OBJDIR)/hu_stuff.o \
		$(OBJDIR)/y_inter.o  \
		$(OBJDIR)/st_stuff.o \
		$(OBJDIR)/m_argv.o   \
		$(OBJDIR)/m_bbox.o   \
		$(OBJDIR)/m_cheat.o  \
		$(OBJDIR)/m_fixed.o  \
		$(OBJDIR)/m_menu.o   \
		$(OBJDIR)/m_misc.o   \
		$(OBJDIR)/m_random.o \
		$(OBJDIR)/m_queue.o  \
		$(OBJDIR)/info.o     \
		$(OBJDIR)/p_ceilng.o \
		$(OBJDIR)/p_enemy.o  \
		$(OBJDIR)/p_fab.o    \
		$(OBJDIR)/p_floor.o  \
		$(OBJDIR)/p_inter.o  \
		$(OBJDIR)/p_lights.o \
		$(OBJDIR)/p_map.o    \
		$(OBJDIR)/p_maputl.o \
		$(OBJDIR)/p_mobj.o   \
		$(OBJDIR)/p_polyobj.o\
		$(OBJDIR)/p_saveg.o  \
		$(OBJDIR)/p_setup.o  \
		$(OBJDIR)/p_sight.o  \
		$(OBJDIR)/p_spec.o   \
		$(OBJDIR)/p_telept.o \
		$(OBJDIR)/p_tick.o   \
		$(OBJDIR)/p_user.o   \
		$(OBJDIR)/tables.o   \
		$(OBJDIR)/r_bsp.o    \
		$(OBJDIR)/r_data.o   \
		$(OBJDIR)/r_draw.o   \
		$(OBJDIR)/r_main.o   \
		$(OBJDIR)/r_plane.o  \
		$(OBJDIR)/r_segs.o   \
		$(OBJDIR)/r_sky.o    \
		$(OBJDIR)/r_splats.o \
		$(OBJDIR)/r_things.o \
		$(OBJDIR)/screen.o   \
		$(OBJDIR)/v_video.o  \
		$(OBJDIR)/s_sound.o  \
		$(OBJDIR)/sounds.o   \
		$(OBJDIR)/w_wad.o    \
		$(OBJDIR)/filesrch.o \
		$(OBJDIR)/mserv.o    \
		$(OBJDIR)/i_tcp.o    \
		$(OBJDIR)/lzf.o	     \
		$(OBJDIR)/vid_copy.o \
		$(i_cdmus_o)    \
		$(i_net_o)      \
		$(i_system_o)   \
		$(i_sound_o)    \
		$(OBJS)

#make a big srb2.s that is the disasm of the exe (dos only ?)
asm:
	$(CC) $(LDFLAGS) $(OBJS) -o $(OBJDIR)/tmp.exe $(LIBS)
	$(OBJDUMP) -d $(OBJDIR)/tmp.exe --no-show-raw-insn > srb2.s
	$(REMOVE) $(OBJDIR)/tmp.exe

# executable
# NOTE: DJGPP's objcopy do not have --add-gnu-debuglink

$(TARGET): $(POS) $(OBJS)
	# -$(MKDIR) $(PKG_EXEC)
	@echo Linking $(TARGET)...
	echo $(LD) $(LDFLAGS) $(OBJS) -o ../$(TARGET) $(LIBS)
	$(LD) $(LDFLAGS) $(OBJS) -o ../$(TARGET) $(LIBS)

	$(OBJCOPY) ../$(TARGET) ../$(TARGET).debug
	$(OBJCOPY) --strip-debug ../$(TARGET)
	$(OBJCOPY) --add-gnu-debuglink=../$(TARGET).debug ../$(TARGET)

reobjdump:
	@echo Redumping debugging info
	$(OBJDUMP) $(OBJDUMP_OPTS) $(PKG_EXEC)/$(DBGNAME) > $(PKG_EXEC)/$(DBGNAME).txt
	-$(GZIP) $(GZIP_OPTS) $(PKG_EXEC)/$(DBGNAME).txt
	-$(GZIP) $(GZIP_OPT2) $(PKG_EXEC)/$(DBGNAME).txt

$(OBJDIR):
	-$(MKDIR) $(OBJDIR)

$(OBJDIR)/r_opengl.o: hardware/r_opengl/r_opengl.c hardware/r_opengl/r_opengl.h \
 doomdef.h doomtype.h g_state.h m_swap.h hardware/hw_drv.h screen.h \
 command.h hardware/hw_data.h hardware/hw_glide.h hardware/hw_defs.h \
 hardware/hw_md2.h hardware/hw_glob.h hardware/hw_main.h am_map.h \
 d_event.h d_player.h p_pspr.h m_fixed.h tables.h info.h d_think.h \
 p_mobj.h doomdata.h d_ticcmd.h r_defs.h hardware/hw_dll.h
	$(CC) $(CFLAGS) $(WFLAGS) -I/usr/X11R6/include -c $< -o $@

#dependecy made by gcc itself !
$(OBJS):
ifndef DUMMY
-include $(OBJDIR)/depend.dep
endif

$(OBJDIR)/depend.dep:
	@echo "Creating dependency file, depend.dep"
	@echo > comptime.h
	-$(MKDIR) $(OBJDIR)
	$(CC) $(CFLAGS) -MM *.c > $(OBJDIR)/depend.ped
	$(CC) $(CFLAGS) -MM $(INTERFACE)/*.c >> $(OBJDIR)/depend.ped
ifndef NOHW
	$(CC) $(CFLAGS) -MM hardware/*.c >> $(OBJDIR)/depend.ped
endif
	@sed -e 's,\(.*\)\.o: ,$(subst /,\/,$(OBJDIR))\/&,g' < $(OBJDIR)/depend.ped > $(OBJDIR)/depend.dep
	$(REMOVE) $(OBJDIR)/depend.ped
	@echo "Created dependency file, depend.dep"

$(OBJDIR)/comptime.o: comptime.c pre-build
	$(CC) $(CFLAGS) $(WFLAGS) -c $< -o $@

$(OBJDIR)/%.o: %.c
	$(CC) $(CFLAGS) $(WFLAGS) -c $< -o $@

$(OBJDIR)/%.o: $(INTERFACE)/%.c
	$(CC) $(CFLAGS) $(WFLAGS) -c $< -o $@

$(OBJDIR)/%.o: hardware/%.c
	$(CC) $(CFLAGS) $(WFLAGS) -c $< -o $@

$(OBJDIR)/%.o: %.nas
	$(NASM) $(NASMOPTS) -o $@ -f $(NASMFORMAT) $<

$(OBJDIR)/vid_copy.o: vid_copy.s asm_defs.inc
	$(CC) $(OPTS) $(ASFLAGS) -x assembler-with-cpp -c $< -o $@

$(OBJDIR)/%.o: %.s
	$(CC) $(OPTS) -x assembler-with-cpp -c $< -o $@

$(OBJDIR)/SRB2.res: win32/Srb2win.rc win32/afxres.h win32/resource.h
	$(WINDRES) -i $< -O rc $(WINDRESFLAGS) --include-dir=win32 -o $@ -O coff

ifdef SDL
$(OBJDIR)/s_fmod.o: hardware/s_fmod/s_fmod.c hardware/hw3dsdrv.h \
 hardware/hw_dll.h
	$(CC) $(M5) -Os -o $(OBJDIR)/s_fmod.o -DHW3SOUND -DUNIXCOMMON -shared -nostartfiles -c hardware/s_fmod/s_fmod.c

$(OBJDIR)/s_openal.o: hardware/s_openal/s_openal.c hardware/hw3dsdrv.h \
 hardware/hw_dll.h
	$(CC) $(M5) -Os -o $(OBJDIR)/s_openal.o -DHW3SOUND -DUNIXCOMMON -shared -nostartfiles -c hardware/s_openal/s_openal.c
endif

#############################################################
#
#############################################################

all:	 pre-build $(TARGET)


cleandep:
	$(REMOVE) $(OBJDIR)/depend.dep
	$(REMOVE) comptime.h

pre-build:
	-@touch comptime.c

clean:
	$(REMOVE) *~ *.flc
	$(REMOVE) $(OBJDIR)/*.o
