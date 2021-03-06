###
# Template base: Nov 4 2013
#

###
# Output executable, file extension (.c/.cpp/.cc),
# source directory (./src), intermediate directory (.o, .d).
f_exe     = prog
c         = cpp
d_src     = .
d_ntm     = tmp

###
# Include directories (-I), library directories (-L),
# library names(-l), specific libraries (../libhello.a).
d_I       = .
d_L       =
d_l       =
f_lx      = ../gnu.make.lib/libltalloc.a

###
# Compilation settings.
# F can be {0:dbg, 1:fast, 2:fast+dbg}
# For readability, c/cxx/cppflags show up before dbg".
cc        = gcc
cxx       = g++
cppflags  = -pthread -Wl,--no-as-needed
cflags    = -std=c11
cxxflags  = -std=c++11

ifneq ($F,0)
cppflags += -O2
else
cppflags += -O0
endif

ifneq ($F,1)
dbgcpp   +=     -ggdb3 -pedantic -Wall -Wextra                                \
                -Wno-switch-default -Wno-sign-compare                         \
                -Wdouble-promotion -Wformat=2 -Wmissing-include-dirs          \
                -Wsync-nand -Wunused -Wuninitialized -Wunknown-pragmas        \
                -Wstrict-overflow=5 -Wtrampolines -Wfloat-equal               \
                -Wundef -Wshadow -Wunsafe-loop-optimizations                  \
                -Wcast-qual -Wcast-align -Wwrite-strings -Wconversion         \
                -Wsign-conversion -Wlogical-op -Wmissing-declarations         \
                -Wno-multichar -Wnormalized=nfc -Wpacked -Wpadded             \
                -Wredundant-decls -Winline -Winvalid-pch                      \
                -Wvector-operation-performance -Wvla                          \
                -Wdisabled-optimization -Wstack-protector                     \
                -Woverlength-strings
dbgc     +=     -Wtraditional-conversion -Wdeclaration-after-statement        \
                -Wbad-function-cast -Wjump-misses-init                        \
                -Wstrict-prototypes -Wold-style-definition                    \
                -Wmissing-prototypes -Wnested-externs                         \
                -Wunsuffixed-float-constants
        # gcc >= 4.8 only
dbgcxx   +=     -Wzero-as-null-pointer-constant                               \
                -Wuseless-cast -Wvarargs
endif



###
# Internals: preprocessing followed by rules
srcs     := $(wildcard $(d_src)/*.$c)
objs     := $(srcs:$(d_src)/%.$c=$(d_ntm)/%.o)
CC       := $(cc)
CXX      := $(cxx)
CFLAGS   := $(strip   $(cflags) $(cppflags) $(dbgcpp) $(dbgc))
CXXFLAGS := $(strip $(cxxflags) $(cppflags) $(dbgcpp) $(dbgcxx))
CPPFLAGS := $(addprefix -I,$(d_I))
LDFLAGS  := $(addprefix -L,$(d_L))
LDLIBS   := $(addprefix -l,$(d_l)) $(strip $(f_lx))

.PHONY: all
all: $(f_exe)
$(f_exe): $(objs)
	$(LINK.$c) $^ $(LOADLIBES) $(LDLIBS) -o $@
$(d_ntm)/%.o: $(d_src)/%.$c
	$(COMPILE.$c) -MD -MP $(OUTPUT_OPTION) $<

.PHONY: clean
clean:
	-rm -fv $(f_exe) $(d_ntm)/prevcfg $(d_ntm)/*.d $(d_ntm)/*.o
	-rmdir --ignore-fail-on-non-empty -p $(d_ntm)

.PHONY: run
run: all
	`readlink -e $(f_exe)` $(runargs)

newcfg   := $(strip $(foreach v,srcs LINK.$c COMPILE.$c LDLIBS,$v:$($v)))
-include $(d_ntm)/prevcfg
ifneq ($(newcfg),$(prevcfg))
.PHONY: force-rebuild
force-rebuild:
	@mkdir -p $(d_ntm)
	@echo prevcfg=$(newcfg) > $(d_ntm)/prevcfg
$(objs): force-rebuild
else
-include $(objs:.o=.d)
endif


