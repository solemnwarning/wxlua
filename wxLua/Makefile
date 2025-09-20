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
	
	LUA ?= lua
else
	# BUILD_LUA is the version of Lua to build.
	LUA_PREREQ := modules/lua-$(BUILD_LUA)/install/lib/liblua.a
	LUA_CFLAGS := -Imodules/lua-$(BUILD_LUA)/install/include/
	LUA_LIBS   := modules/lua-$(BUILD_LUA)/install/lib/liblua.a -ldl
	
	ifeq ($(BUILD_LUA),5.2)
		LUA_CFLAGS += -DLUA_COMPAT_MODULE
	endif
	
	LUA := $(shell pwd)/modules/lua-$(BUILD_LUA)/install/bin/lua
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

ifeq ($(OS),Windows_NT)
	LUAMODULE := lib/lua/wx.dll
	WX_LIBS += -lws2_32
else
	LUAMODULE := lib/lua/wx.so
endif

LUAMODULE_OBJS := \
	modules/luamodule/luamodule.o

# Bindings which must be built before anything in wxLua is compiled
BINDING_PREREQS := \
	$(WXBASE_BINDING_TARGETS) \
	$(WXCORE_BINDING_TARGETS) \
	$(foreach x,$(WXBIND_BASIC_BINDINGS),include/wxbind/$(x)_bind.h) \
	include/wxlua/wxlua_bind.h \
	modules/wxlua/debugger/wxluadebugger_bind.h \
	apps/wxluacan/wxluacan_bind.h

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
	
	rm -f $(WXBASE_BINDING_TARGETS) $(WXBASE_BINDING_FLAG_FILE)
	rm -f $(WXCORE_BINDING_TARGETS) $(WXCORE_BINDING_FLAG_FILE)
	rm -f $(foreach x,$(WXBIND_BASIC_BINDINGS),bindings/wxwidgets/$(x)_datatypes.lua)
	rm -f $(foreach x,$(WXBIND_BASIC_BINDINGS),include/wxbind/$(x)_bind.h)
	rm -f $(foreach x,$(WXBIND_BASIC_BINDINGS),modules/wxbind/src/$(x)_bind.cpp)
	rm -f bindings/wxwidgets/wx_datatypes.lua
	
	rm -f include/wxlua/wxlua_bind.h bindings/wxlua/wxlua_datatypes.lua modules/wxlua/wxlua_bind.cpp
	rm -f modules/wxlua/debugger/wxluadebugger_bind.h bindings/wxlua_debugger/wxluadebugger_datatypes.lua modules/wxlua/debugger/wxluadebugger_bind.cpp
	rm -f apps/wxluacan/wxluacan_bind.h apps/wxluacan/wxluacan_datatypes.lua apps/wxluacan/wxluacan_bind.cpp
	
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

%.o: %.c $(LUA_PREREQ) $(BINDING_PREREQS)
	$(CC) -Iinclude/ -Imodules/wxbind/setup/ $(LUA_CFLAGS) $(CFLAGS) -fPIC -c -o $@ $<

%.o: %.cpp $(LUA_PREREQ) $(BINDING_PREREQS)
	$(CXX) -Iinclude/ -Imodules/wxbind/setup/ $(LUA_CFLAGS) $(WX_CXXFLAGS) $(CXXFLAGS) -fPIC -c -o $@ $<

modules/lua-5.1/install/lib/liblua.a:
	# Lua's Makefile work with >1 concurrency.
	$(MAKE) -j1 -C modules/lua-5.1/ linux install INSTALL_TOP="$$(pwd)/modules/lua-5.1/install/"

modules/lua-5.2/install/lib/liblua.a:
	# Lua's Makefile work with >1 concurrency.
	$(MAKE) -j1 -C modules/lua-5.2/ CFLAGS="-DLUA_COMPAT_MODULE -DLUA_USE_LINUX -fPIC" linux local
	touch $@

# wxWidgets library bindings

# The wxbase bindings produce multiple source files from a single command, this
# would ideally be handled as a Grouped Target, but those weren't introduced
# until GNU Make 4.3, which I'm not happy depending on yet so we go for the old
# faithful all-the-outputs-depend-on-a-dummy-target-which-actually-makes-them
# approach. If any of the targets are missing, then we flag that dummy as a
# phony target to ensure it gets run to build the missing file(s).

WXBASE_BINDING_TARGETS := \
	bindings/wxwidgets/wxbase_datatypes.lua \
	include/wxbind/wxbase_bind.h \
	modules/wxbind/src/wxbase_base.cpp \
	modules/wxbind/src/wxbase_bind.cpp \
	modules/wxbind/src/wxbase_config.cpp \
	modules/wxbind/src/wxbase_data.cpp \
	modules/wxbind/src/wxbase_datetime.cpp \
	modules/wxbind/src/wxbase_file.cpp

WXBASE_BINDING_DEPENDENCIES := \
	bindings/wxwidgets/wxbase_rules.lua \
	bindings/wxwidgets/wxbase_base.i \
	bindings/wxwidgets/wxbase_config.i \
	bindings/wxwidgets/wxbase_data.i \
	bindings/wxwidgets/wxbase_datetime.i \
	bindings/wxwidgets/wxbase_file.i \
	bindings/wxwidgets/wxbase_override.hpp

WXBASE_BINDING_FLAG_FILE := \
	bindings/wxwidgets/wxbase_bind.flag

ifneq ($(strip $(strip $(foreach x,$(WXBASE_BINDING_TARGETS),$(if $(wildcard $(x)),,$(x))))),)
.PHONY: $(WXBASE_BINDING_FLAG_FILE)
endif

$(WXBASE_BINDING_TARGETS): $(WXBASE_BINDING_FLAG_FILE)

$(WXBASE_BINDING_FLAG_FILE): $(WXBASE_BINDING_DEPENDENCIES) $(LUA_PREREQ)
	cd bindings/ && $(LUA) -e"rulesFilename=\"wxwidgets/wxbase_rules.lua\"" genwxbind.lua
	
	# genwxbind.lua won't update files that don't need it, so touch all the
	# targets so that Make knows they are up-to-date.
	touch $(WXBASE_BINDING_TARGETS)
	
	touch $@

# The wxcore bindings produce multiple source files from a single command, this
# would ideally be handled as a Grouped Target, but those weren't introduced
# until GNU Make 4.3, which I'm not happy depending on yet so we go for the old
# faithful all-the-outputs-depend-on-a-dummy-target-which-actually-makes-them
# approach. If any of the targets are missing, then we flag that dummy as a
# phony target to ensure it gets run to build the missing file(s).

WXCORE_BINDING_TARGETS := \
	bindings/wxwidgets/wxcore_datatypes.lua \
	include/wxbind/wxcore_bind.h \
	modules/wxbind/src/wxcore_appframe.cpp \
	modules/wxbind/src/wxcore_bind.cpp \
	modules/wxbind/src/wxcore_clipdrag.cpp \
	modules/wxbind/src/wxcore_controls.cpp \
	modules/wxbind/src/wxcore_core.cpp \
	modules/wxbind/src/wxcore_defsutils.cpp \
	modules/wxbind/src/wxcore_dialogs.cpp \
	modules/wxbind/src/wxcore_event.cpp \
	modules/wxbind/src/wxcore_gdi.cpp \
	modules/wxbind/src/wxcore_geometry.cpp \
	modules/wxbind/src/wxcore_graphics.cpp \
	modules/wxbind/src/wxcore_help.cpp \
	modules/wxbind/src/wxcore_image.cpp \
	modules/wxbind/src/wxcore_mdi.cpp \
	modules/wxbind/src/wxcore_menutool.cpp \
	modules/wxbind/src/wxcore_picker.cpp \
	modules/wxbind/src/wxcore_print.cpp \
	modules/wxbind/src/wxcore_sizer.cpp \
	modules/wxbind/src/wxcore_windows.cpp

WXCORE_BINDING_DEPDENDENCIES := \
	bindings/wxwidgets/wxbase_datatypes.lua \
	bindings/wxwidgets/wxcore_rules.lua \
	bindings/wxwidgets/wxcore_appframe.i \
	bindings/wxwidgets/wxcore_clipdrag.i \
	bindings/wxwidgets/wxcore_controls.i \
	bindings/wxwidgets/wxcore_core.i \
	bindings/wxwidgets/wxcore_defsutils.i \
	bindings/wxwidgets/wxcore_dialogs.i \
	bindings/wxwidgets/wxcore_event.i \
	bindings/wxwidgets/wxcore_gdi.i \
	bindings/wxwidgets/wxcore_geometry.i \
	bindings/wxwidgets/wxcore_graphics.i \
	bindings/wxwidgets/wxcore_help.i \
	bindings/wxwidgets/wxcore_image.i \
	bindings/wxwidgets/wxcore_mdi.i \
	bindings/wxwidgets/wxcore_menutool.i \
	bindings/wxwidgets/wxcore_override.hpp \
	bindings/wxwidgets/wxcore_picker.i \
	bindings/wxwidgets/wxcore_print.i \
	bindings/wxwidgets/wxcore_sizer.i \
	bindings/wxwidgets/wxcore_windows.i

WXCORE_BINDING_FLAG_FILE := \
	bindings/wxwidgets/wxcore_bind.flag

ifneq ($(strip $(strip $(foreach x,$(WXCORE_BINDING_TARGETS),$(if $(wildcard $(x)),,$(x))))),)
.PHONY: $(WXCORE_BINDING_FLAG_FILE)
endif

$(WXCORE_BINDING_TARGETS): $(WXCORE_BINDING_FLAG_FILE)

$(WXCORE_BINDING_FLAG_FILE): $(WXCORE_BINDING_DEPDENDENCIES) $(LUA_PREREQ)
	cd bindings/ && $(LUA) -e"rulesFilename=\"wxwidgets/wxcore_rules.lua\"" genwxbind.lua
	
	# genwxbind.lua won't update files that don't need it, so touch all the
	# targets so that Make knows they are up-to-date.
	touch $(WXCORE_BINDING_TARGETS)
	
	touch $@

# The "basic" wxWidgets bindings all follow the same pattern and have a
# consistent set of outputs which can be encompassed by a pattern rule.

WXBIND_BASIC_BINDINGS ?= \
	wxadv \
	wxaui \
	wxgl \
	wxhtml \
	wxmedia \
	wxnet \
	wxpropgrid \
	wxrichtext \
	wxstc \
	wxwebview \
	wxxml \
	wxxrc

include/wxbind/%_bind.h bindings/wxwidgets/%_datatypes.lua modules/wxbind/src/%_bind.cpp: bindings/wxwidgets/%_rules.lua $(LUA_PREREQ)
	cd bindings/ && $(LUA) -e"rulesFilename=\"wxwidgets/$*_rules.lua\"" genwxbind.lua
	
	# genwxbind.lua won't update files that don't need it, so touch all the
	# targets so that Make knows they are up-to-date.
	touch include/wxbind/$*_bind.h \
		bindings/wxwidgets/$*_datatypes.lua \
		modules/wxbind/src/$*_bind.cpp

# Merged data types from wxWidgets library bindings for use by other bindings
bindings/wxwidgets/wx_datatypes.lua: bindings/wxwidgets/wxbase_datatypes.lua $(foreach x,$(WXBIND_BASIC_BINDINGS),bindings/wxwidgets/$(x)_datatypes.lua) $(LUA_PREREQ)
	cd bindings/ && $(LUA) -e"rulesFilename=\"wxwidgets/wxdatatypes_rules.lua\"" genwxbind.lua
	
	# genwxbind.lua won't update files that don't need it, so touch all the
	# targets so that Make knows they are up-to-date.
	touch $@

# Individual library/application bindings.
# These are pattern rules despite being for single targets so that we can
# indicate they produce multiple outputs.

include/wxlua/%_bind.h bindings/wxlua/%_datatypes.lua modules/wxlua/%_bind.cpp: bindings/wxlua/wxlua_rules.lua $(LUA_PREREQ)
	cd bindings/ && $(LUA) -e"rulesFilename=\"wxlua/$*_rules.lua\"" genwxbind.lua
	
	# genwxbind.lua won't update files that don't need it, so touch all the
	# targets so that Make knows they are up-to-date.
	touch include/wxlua/$*_bind.h \
		bindings/wxlua/$*_datatypes.lua \
		modules/wxlua/$*_bind.cpp

modules/wxlua/debugger/%_bind.h bindings/wxlua_debugger/%_datatypes.lua modules/wxlua/debugger/%_bind.cpp: bindings/wxlua_debugger/%_rules.lua $(LUA_PREREQ)
	cd bindings/ && $(LUA) -e"rulesFilename=\"wxlua_debugger/$*_rules.lua\"" genwxbind.lua
	
	# genwxbind.lua won't update files that don't need it, so touch all the
	# targets so that Make knows they are up-to-date.
	touch modules/wxlua/debugger/$*_bind.h \
		bindings/wxlua_debugger/$*_datatypes.lua \
		modules/wxlua/debugger/$*_bind.cpp

apps/wxluacan/%_bind.h apps/wxluacan/%_datatypes.lua apps/wxluacan/%_bind.cpp: apps/wxluacan/%_rules.lua $(LUA_PREREQ)
	cd apps/wxluacan/ && $(LUA) -e"rulesFilename=\"$*_rules.lua\"" ../../bindings/genwxbind.lua
	
	# genwxbind.lua won't update files that don't need it, so touch all the
	# targets so that Make knows they are up-to-date.
	touch apps/wxluacan/$*_bind.h \
		apps/wxluacan/$*_datatypes.lua \
		apps/wxluacan/$*_bind.cpp

# Additional dependencies of the various bindings.
# TODO: Update genwxbind.lua to output Makefile fragments with all the
# dependencies and include those so we only need an explicit dependency on the
# rules file instead.

include/wxbind/wxadv_bind.h bindings/wxwidgets/wxadv_datatypes.lua modules/wxbind/src/wxadv_bind.cpp: \
	bindings/wxwidgets/wxadv_adv.i \
	bindings/wxwidgets/wxadv_dataview.i \
	bindings/wxwidgets/wxadv_dvrenderers.i \
	bindings/wxwidgets/wxadv_grid.i \
	bindings/wxwidgets/wxadv_override.hpp \
	bindings/wxwidgets/wxcore_datatypes.lua

include/wxbind/wxaui_bind.h bindings/wxwidgets/wxaui_datatypes.lua modules/wxbind/src/wxaui_bind.cpp: \
	bindings/wxwidgets/wxaui_aui.i \
	bindings/wxwidgets/wxaui_override.hpp \
	bindings/wxwidgets/wxcore_datatypes.lua

include/wxbind/wxgl_bind.h bindings/wxwidgets/wxgl_datatypes.lua modules/wxbind/src/wxgl_bind.cpp: \
	bindings/wxwidgets/wxcore_datatypes.lua \
	bindings/wxwidgets/wxgl_gl.i

include/wxbind/wxhtml_bind.h bindings/wxwidgets/wxhtml_datatypes.lua modules/wxbind/src/wxhtml_bind.cpp: \
	bindings/wxwidgets/wxcore_datatypes.lua \
	bindings/wxwidgets/wxhtml_html.i \
	bindings/wxwidgets/wxhtml_override.hpp

include/wxbind/wxmedia_bind.h bindings/wxwidgets/wxmedia_datatypes.lua modules/wxbind/src/wxmedia_bind.cpp: \
	bindings/wxwidgets/wxcore_datatypes.lua \
	bindings/wxwidgets/wxnet_datatypes.lua \
	bindings/wxwidgets/wxmedia_media.i

include/wxbind/wxnet_bind.h bindings/wxwidgets/wxnet_datatypes.lua modules/wxbind/src/wxnet_bind.cpp: \
	bindings/wxwidgets/wxcore_datatypes.lua \
	bindings/wxwidgets/wxnet_net.i \
	bindings/wxwidgets/wxnet_override.hpp

include/wxbind/wxpropgrid_bind.h bindings/wxwidgets/wxpropgrid_datatypes.lua modules/wxbind/src/wxpropgrid_bind.cpp: \
	bindings/wxwidgets/wxcore_datatypes.lua \
	bindings/wxwidgets/wxpropgrid_override.hpp \
	bindings/wxwidgets/wxpropgrid_propgrid.i

include/wxbind/wxrichtext_bind.h bindings/wxwidgets/wxrichtext_datatypes.lua modules/wxbind/src/wxrichtext_bind.cpp: \
	bindings/wxwidgets/wxcore_datatypes.lua \
	bindings/wxwidgets/wxrichtext_override.hpp \
	bindings/wxwidgets/wxrichtext_print.i \
	bindings/wxwidgets/wxrichtext_richtext.i \
	bindings/wxwidgets/wxrichtext_style.i \
	bindings/wxwidgets/wxrichtext_symboldlg.i \
	bindings/wxwidgets/wxrichtext_xml.i \
	bindings/wxwidgets/wxxml_datatypes.lua

include/wxbind/wxstc_bind.h bindings/wxwidgets/wxstc_datatypes.lua modules/wxbind/src/wxstc_bind.cpp: \
	bindings/wxwidgets/wxcore_datatypes.lua \
	bindings/wxwidgets/wxstc_override.hpp \
	bindings/wxwidgets/wxstc_stc.i

include/wxbind/wxwebview_bind.h bindings/wxwidgets/wxwebview_datatypes.lua modules/wxbind/src/wxwebview_bind.cpp: \
	bindings/wxwidgets/wxcore_datatypes.lua \
	bindings/wxwidgets/wxwebview_webview.i

include/wxbind/wxxml_bind.h bindings/wxwidgets/wxxml_datatypes.lua modules/wxbind/src/wxxml_bind.cpp: \
	bindings/wxwidgets/wxbase_datatypes.lua \
	bindings/wxwidgets/wxxml_override.hpp \
	bindings/wxwidgets/wxxml_xml.i

include/wxbind/wxxrc_bind.h bindings/wxwidgets/wxxrc_datatypes.lua modules/wxbind/src/wxxrc_bind.cpp: \
	bindings/wxwidgets/wxcore_datatypes.lua \
	bindings/wxwidgets/wxxrc_xrc.i

include/wxlua/wxlua_bind.h bindings/wxlua/wxlua_datatypes.lua modules/wxlua/wxlua_bind.cpp: \
	bindings/wxwidgets/wxbase_datatypes.lua \
	bindings/wxlua/override.hpp \
	bindings/wxlua/wxlua.i

modules/wxlua/debugger/wxluadebugger_bind.h bindings/wxlua_debugger/wxluadebugger_datatypes.lua modules/wxlua/debugger/wxluadebugger_bind.cpp: \
	bindings/wxwidgets/wxcore_datatypes.lua \
	bindings/wxlua_debugger/override.hpp \
	bindings/wxlua_debugger/wxluadebugger.i

apps/wxluacan/wxluacan_bind.h apps/wxluacan/wxluacan_datatypes.lua apps/wxluacan/wxluacan_bind.cpp: \
	apps/wxluacan/wxluacan.i \
	bindings/wxwidgets/wx_datatypes.lua
