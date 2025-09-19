#----------------------------------------------------------------------------
# Purpose:     wxLua Makefile
# Author:      Daniel Collins
# Created:     19/09/2025
# Copyright:   (c) 2025 Daniel Collins
# Licence:     wxWidgets licence
#----------------------------------------------------------------------------

# Wrapper around the $(shell) function that aborts the build if the command
# exits with a nonzero status.
shell-or-die = $\
	$(eval sod_out := $$(shell $(1); echo $$$$?))$\
	$(if $(filter 0,$(lastword $(sod_out))),$\
		$(wordlist 1, $(shell echo $$(($(words $(sod_out)) - 1))), $(sod_out)),$\
		$(error $(1) exited with status $(lastword $(sod_out))))

# Takes a list of space-separated package names and returns the first which is
# defined in the pkg-config database, or errors if none are.
pkg-select = $\
	$(eval found_pkg := $$(firstword $$(foreach pkg,$(1), $$(shell pkg-config --exists $$(pkg) && echo $$(pkg))))) $\
	$(if $(found_pkg),$(found_pkg),$(error Could not found any of the following packages using pkg-config: $(1)))

WX_CONFIG ?= wx-config
WX_USE_LIBS ?= propgrid webview gl xrc xml net media richtext aui stc html adv core base

WX_CXXFLAGS ?= $(call shell-or-die,$(WX_CONFIG) --cxxflags $(WX_USE_LIBS))
WX_LIBS     ?= $(call shell-or-die,$(WX_CONFIG) --libs $(WX_USE_LIBS))

BUILD_LUA ?= 5.1

ifeq ($(filter-out 0,$(BUILD_LUA)),)
	# BUILD_LUA is 0 or empty - use the system Lua.
	LUA_PKG    ?= $(call pkg-select,lua5.4 lua5.3 lua5.2 lua5.1 lua)
	LUA_CFLAGS ?= $(call shell-or-die,pkg-config $(LUA_PKG) --cflags)
	LUA_LIBS   ?= $(call shell-or-die,pkg-config $(LUA_PKG) --libs)
else
	# BUILD_LUA is the version of Lua to build.
	LUA_PREREQ := modules/lua-$(BUILD_LUA)/install/lib/liblua.a
	LUA_CFLAGS := -Imodules/lua-$(BUILD_LUA)/install/include/
	LUA_LIBS   := modules/lua-$(BUILD_LUA)/install/lib/liblua.a -ldl
	
	ifeq ($(BUILD_LUA),5.2)
		LUA_CFLAGS += -DLUA_COMPAT_MODULE
	endif
endif

WXLUA_DIR := modules/wxlua

WXLUA_LIB := lib/wxlua.a
WXLUA_OBJS := \
	$(WXLUA_DIR)/bit.o \
	$(WXLUA_DIR)/lbitlib.o \
	$(WXLUA_DIR)/wxlbind.o \
	$(WXLUA_DIR)/wxlcallb.o \
	$(WXLUA_DIR)/wxlconsole.o \
	$(WXLUA_DIR)/wxllua.o \
	$(WXLUA_DIR)/wxlobject.o \
	$(WXLUA_DIR)/wxlstate.o \
	$(WXLUA_DIR)/wxlua_bind.o

WXLUA_DEBUG_LIB := lib/wxlua-debug.a
WXLUA_DEBUG_OBJS := \
	$(WXLUA_DIR)/debug/wxldebug.o \
	$(WXLUA_DIR)/debug/wxlstack.o

WXLUA_DEBUGGER_LIB := lib/wxlua-debugger.a
WXLUA_DEBUGGER_OBJS := \
	$(WXLUA_DIR)/debugger/wxldserv.o \
	$(WXLUA_DIR)/debugger/wxldtarg.o \
	$(WXLUA_DIR)/debugger/wxlsock.o \
	$(WXLUA_DIR)/debugger/wxluadebugger_bind.o

WXBIND_DIR := modules/wxbind

WXBIND_LIB := lib/wxbind.a
WXBIND_OBJS := \
	$(WXBIND_DIR)/src/wxadv_bind.o \
	$(WXBIND_DIR)/src/wxadv_wxladv.o \
	$(WXBIND_DIR)/src/wxaui_bind.o \
	$(WXBIND_DIR)/src/wxbase_base.o \
	$(WXBIND_DIR)/src/wxbase_bind.o \
	$(WXBIND_DIR)/src/wxbase_config.o \
	$(WXBIND_DIR)/src/wxbase_data.o \
	$(WXBIND_DIR)/src/wxbase_datetime.o \
	$(WXBIND_DIR)/src/wxbase_file.o \
	$(WXBIND_DIR)/src/wxcore_appframe.o \
	$(WXBIND_DIR)/src/wxcore_bind.o \
	$(WXBIND_DIR)/src/wxcore_clipdrag.o \
	$(WXBIND_DIR)/src/wxcore_controls.o \
	$(WXBIND_DIR)/src/wxcore_core.o \
	$(WXBIND_DIR)/src/wxcore_defsutils.o \
	$(WXBIND_DIR)/src/wxcore_dialogs.o \
	$(WXBIND_DIR)/src/wxcore_event.o \
	$(WXBIND_DIR)/src/wxcore_gdi.o \
	$(WXBIND_DIR)/src/wxcore_geometry.o \
	$(WXBIND_DIR)/src/wxcore_graphics.o \
	$(WXBIND_DIR)/src/wxcore_help.o \
	$(WXBIND_DIR)/src/wxcore_image.o \
	$(WXBIND_DIR)/src/wxcore_mdi.o \
	$(WXBIND_DIR)/src/wxcore_menutool.o \
	$(WXBIND_DIR)/src/wxcore_picker.o \
	$(WXBIND_DIR)/src/wxcore_print.o \
	$(WXBIND_DIR)/src/wxcore_sizer.o \
	$(WXBIND_DIR)/src/wxcore_windows.o \
	$(WXBIND_DIR)/src/wxcore_wxlcore.o \
	$(WXBIND_DIR)/src/wxgl_bind.o \
	$(WXBIND_DIR)/src/wxhtml_bind.o \
	$(WXBIND_DIR)/src/wxhtml_wxlhtml.o \
	$(WXBIND_DIR)/src/wxmedia_bind.o \
	$(WXBIND_DIR)/src/wxnet_bind.o \
	$(WXBIND_DIR)/src/wxpropgrid_bind.o \
	$(WXBIND_DIR)/src/wxrichtext_bind.o \
	$(WXBIND_DIR)/src/wxstc_bind.o \
	$(WXBIND_DIR)/src/wxwebview_bind.o \
	$(WXBIND_DIR)/src/wxxml_bind.o \
	$(WXBIND_DIR)/src/wxxrc_bind.o

WXLUA_APP := bin/wxlua
WXLUA_APP_OBJS := \
	apps/wxlua/wxlua.o

WXLUACAN_APP := bin/wxluacan
WXLUACAN_APP_OBJS := \
	apps/wxluacan/cancom.o \
	apps/wxluacan/canlua.o \
	apps/wxluacan/cansim.o \
	apps/wxluacan/wxluacan_bind.o

WXLUAFREEZE_APP := bin/wxluafreeze
WXLUAFREEZE_OBJS := \
	apps/wxluafreeze/wxluafreeze.o

WXLUAEDIT_APP := bin/wxluaedit
WXLUAEDIT_OBJS := \
	apps/wxluaedit/wxledit.o \
	apps/wxluaedit/wxluaedit.o

LUAMODULE := lib/lua/wx.so
LUAMODULE_OBJS := \
	modules/luamodule/luamodule.o

.PHONY: all
all: $(WXLUA_LIB) $(WXLUA_DEBUG_LIB) $(WXLUA_DEBUGGER_LIB) $(WXBIND_LIB) $(WXLUA_APP) $(WXLUACAN_APP) $(WXLUAFREEZE_APP) $(LUAMODULE)

.PHONY: clean
clean:
	rm -f $(WXLUA_APP) $(WXLUA_APP_OBJS)
	rm -f $(WXLUACAN_APP) $(WXLUACAN_APP_OBJS)
	rm -f $(WXLUAFREEZE_APP) $(WXLUAFREEZE_OBJS)
	rm -f $(WXLUAEDIT_APP) $(WXLUAEDIT_OBJS)
	rm -f $(WXLUA_LIB) $(WXLUA_OBJS)
	rm -f $(WXLUA_DEBUG_LIB) $(WXLUA_DEBUG_OBJS)
	rm -f $(WXLUA_DEBUGGER_LIB) $(WXLUA_DEBUGGER_OBJS)
	rm -f $(WXBIND_LIB) $(WXBIND_OBJS)
	rm -f $(LUAMODULE) $(LUAMODULE_OBJS)
	rm -f wxluafreeze $(WXLUAFREEZE_OBJS)
	
	$(MAKE) -C modules/lua-5.1/ clean
	rm -rf modules/lua-5.1/install/
	
	$(MAKE) -C modules/lua-5.2/ clean
	rm -rf modules/lua-5.2/install/

$(WXLUA_APP): $(WXLUA_APP_OBJS) $(WXLUA_LIB) $(WXLUA_DEBUG_LIB) $(WXLUA_DEBUGGER_LIB) $(WXBIND_LIB)
	$(CXX) -o $@ $(WXLUA_APP_OBJS) $(WXLUA_LIB) $(WXLUA_DEBUGGER_LIB) $(WXLUA_DEBUG_LIB) $(WXBIND_LIB) $(LUA_LIBS) $(WX_LIBS)

$(WXLUACAN_APP): $(WXLUACAN_APP_OBJS) $(WXLUA_LIB) $(WXBIND_LIB)
	$(CXX) -o $@ $(WXLUACAN_APP_OBJS) $(WXLUA_LIB) $(WXBIND_LIB) $(LUA_LIBS) $(WX_LIBS)

$(WXLUAFREEZE_APP): $(WXLUAFREEZE_OBJS) $(WXLUA_LIB) $(WXLUA_DEBUG_LIB) $(WXLUA_DEBUGGER_LIB) $(WXBIND_LIB)
	$(CXX) -o $@ $(WXLUAFREEZE_OBJS) $(WXLUA_LIB) $(WXLUA_DEBUGGER_LIB) $(WXLUA_DEBUG_LIB) $(WXBIND_LIB) $(LUA_LIBS) $(WX_LIBS)

$(WXLUAEDIT_APP): $(WXLUAEDIT_OBJS)
	$(CXX) -o $@ $(WXLUAEDIT_OBJS)

$(WXLUA_LIB): $(WXLUA_OBJS)
	@mkdir -p $(dir $@)
	ar rcs $@ $(WXLUA_OBJS)

$(WXLUA_DEBUG_LIB): $(WXLUA_DEBUG_OBJS)
	@mkdir -p $(dir $@)
	ar rcs $@ $(WXLUA_DEBUG_OBJS)

$(WXLUA_DEBUGGER_LIB): $(WXLUA_DEBUGGER_OBJS)
	@mkdir -p $(dir $@)
	ar rcs $@ $(WXLUA_DEBUGGER_OBJS)

$(WXBIND_LIB): $(WXBIND_OBJS)
	@mkdir -p $(dir $@)
	ar rcs $@ $(WXBIND_OBJS)

$(LUAMODULE): $(LUAMODULE_OBJS) $(WXLUA_LIB) $(WXLUA_DEBUGGER_LIB) $(WXLUA_DEBUG_LIB) $(WXBIND_LIB)
	@mkdir -p $(dir $@)
	$(CXX) -o $@ -shared -fPIC $(CXXFLAGS) $(LUAMODULE_OBJS) $(WXLUA_LIB) $(WXLUA_DEBUGGER_LIB) $(WXLUA_DEBUG_LIB) $(WXBIND_LIB) $(LUA_LIBS) $(WX_LIBS)

%.o: %.c $(LUA_PREREQ)
	$(CC) -Iinclude/ -Imodules/wxbind/setup/ $(LUA_CFLAGS) $(CFLAGS) -fPIC -c -o $@ $<

%.o: %.cpp $(LUA_PREREQ)
	$(CXX) -Iinclude/ -Imodules/wxbind/setup/ $(LUA_CFLAGS) $(WX_CXXFLAGS) $(CXXFLAGS) -fPIC -c -o $@ $<

modules/lua-5.1/install/lib/liblua.a:
	# Lua's Makefile work with >1 concurrency.
	$(MAKE) -j1 -C modules/lua-5.1/ linux install INSTALL_TOP="$$(pwd)/modules/lua-5.1/install/"

modules/lua-5.2/install/lib/liblua.a:
	# Lua's Makefile work with >1 concurrency.
	$(MAKE) -j1 -C modules/lua-5.2/ CFLAGS="-DLUA_COMPAT_MODULE -DLUA_USE_LINUX -fPIC" linux local
	touch $@
