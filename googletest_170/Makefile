###
# Template base: Jan 15 2014
# TODO: - option to rebuild files that generated warnings.
#


###
# Output file (<none=exe>/.a/.so), source extension (.c/.cpp/.cc),
# source directory (./src), intermediate directory (.o, .d).
f_out     = gtest_main170.a
c         = cpp
d_src     = src
d_ntm     = tmp

###
# Include directories (-I), library directories (-L),
# library names (-l), specific standalones (../libhello.a).
d_I       = . include #/usr/include/x86_64-linux-gnu/c++/4.8
d_L       =
f_l       =
f_lx      =

###
# Compilation settings.
# <F> can be -- 0:dbg, 1:fast, 2:fast+dbg
# For readability, <c/cxx/cppflags> show up before <dbg> ones.
cc        = gcc
cxx       = g++
cppflags  = #-pthread -Wl,--no-as-needed
            #-fPIC
cxxflags  =



F         = 1
family   := $(if $(filter $c,c),$(cc),$(cxx))
o        := $(if $(findstring $F,0),-O0,-O2)
# Non-empty if an element in $2 is a prefix of $1.
prematch  = $(strip $(foreach p,$2,$(findstring ^$p,^$1)))

ifneq ($F,1)
ifneq ($(call prematch,$(family),gcc g++),)
dbgcpp    = -ggdb3 -pedantic -Wall -Wextra                                    \
            -Wno-switch-default -Wno-sign-compare -Wdouble-promotion          \
            -Wformat=2 -Wmissing-include-dirs -Wsync-nand -Wunused            \
            -Wuninitialized -Wunknown-pragmas -Wstrict-overflow=5             \
            -Wtrampolines -Wfloat-equal -Wundef -Wshadow                      \
            -Wunsafe-loop-optimizations -Wcast-qual -Wcast-align              \
            -Wwrite-strings -Wconversion -Wsign-conversion -Wlogical-op       \
            -Wmissing-declarations -Wno-multichar -Wnormalized=nfc            \
            -Wpacked -Wpadded -Wredundant-decls -Winline -Winvalid-pch        \
            -Wvector-operation-performance -Wvla -Wdisabled-optimization      \
            -Wstack-protector -Woverlength-strings
dbgc      = -Wtraditional-conversion -Wdeclaration-after-statement            \
            -Wbad-function-cast -Wjump-misses-init -Wstrict-prototypes        \
            -Wold-style-definition -Wmissing-prototypes -Wnested-externs      \
            -Wunsuffixed-float-constants
dbgcxx    = -Wzero-as-null-pointer-constant -Wuseless-cast -Wvarargs
endif
ifneq ($(call prematch,$(family),clang),)
dbgcpp    = -Weverything -Wno-c++98-compat
endif
endif



###
# Internals: preprocessing followed by rules.
srcs     := $(wildcard $(d_src)/*.$c)
objs     := $(srcs:$(d_src)/%.$c=$(d_ntm)/%.o)
outext   := $(suffix $(f_out))
CC       := $(cc)
CXX      := $(cxx)
CFLAGS   := $(strip   $(cflags) $o $(cppflags) $(dbgcpp) $(dbgc))
CXXFLAGS := $(strip $(cxxflags) $o $(cppflags) $(dbgcpp) $(dbgcxx))
CPPFLAGS := $(addprefix -I,$(d_I))
LDFLAGS  := $(addprefix -L,$(d_L))
LDLIBS   := $(addprefix -l,$(f_l)) $(strip $(f_lx))

.DELETE_ON_ERROR: $(f_out) $(objs)

.PHONY: all
all: $(f_out)

.PHONY: clean
clean:
	-rm -fv $(f_out) $(d_ntm)/prevcfg $(d_ntm)/*.d $(d_ntm)/*.o
	-rmdir --ignore-fail-on-non-empty -p $(d_ntm)

.PHONY: run
run: all
	`readlink -e $(f_out)` $(runargs)

$(f_out): $(objs)
ifeq ($(outext),)
	$(LINK.$c) $^ $(LOADLIBES) $(LDLIBS) -o $@
endif
ifeq ($(outext),.a)
	-rm -fv $@
	$(AR) $(ARFLAGS) $@ $^
endif
ifeq ($(outext),.so)
	$(LINK.$c) -shared $^ -o $@
endif

$(d_ntm)/%.o: $(d_src)/%.$c
	$(COMPILE.$c) -MD -MP $(OUTPUT_OPTION) $<



###
# Rebuild everything if the configuration has changed.
# Otherwise, load file dependencies from previous run.
# An alternative is to store the intermediates of each config
# separately, using md5sum to generate unique subdirectories.
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


