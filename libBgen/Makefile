.PHONY: all release debug profile
all: release 

LIB = lib-bgen.a
LIB_DBG = lib-dbg-bgen.a
BASE = BGenFile BGenIndex BGenVariant
OBJ = $(BASE:=.o)
OBJ_DBG = $(BASE:%=%_dbg.o)

ifndef ROOT
ROOT?=..
endif

TABIX_INC = ../third/tabix
TABIX_LIB = ../third/tabix/libtabix.a
$(TABIX_INC) $(TABIX_LIB):
	(cd ../third; $(MAKE) tabix)

PCRE_INC = ../third/pcre/include
$(PCRE_INC):
	(cd ../third; $(MAKE) pcre)

GSL_INC = ../third/gsl/include
$(GSL_INC):
	(cd ../third; $(MAKE) gsl)

SAMTOOLS_INC = ../third/samtools
BCF_INC = ../third/samtools/bcftools 
$(SAMTOOLS_INC) $(BCF_INC):
	(cd ../third; $(MAKE) samtools)

ZLIB_INC = ../third/zlib
$(ZLIB_INC):
	(cd ../third; $(MAKE) zlib)

BZIP2_INC = ../third/bzip2
$(BZIP2_INC):
	(cd ../third; $(MAKE) bzip2)

SQLITE_INC = ../third/sqlite/include
SQLITE_LIB = ../third/sqlite/lib/libsqlite3.a
$(SQLITE_INC) $(SQLITE_LIB):
	(cd ../third; $(MAKE) sqlite)

ZSTD_INC = ../third/zstd/lib
ZSTD_LIB = ../third/zstd/lib/libzstd.a
$(ZSTD_INC) $(ZSTD_LIB):
	(cd ../third; $(MAKE) zstd)

THIRD_INC = $(BCF_INC) $(TABIX_INC) $(PCRE_INC) $(BZIP2_INC) $(ZLIB_INC) $(GSL_INC) $(ZSTD_INC) $(SQLITE_INC)
THIRD_INC_FLAGS = $(addprefix -I,$(THIRD_INC))

DEFAULT_CXXFLAGS ?= -std=c++0x -I. -I$(ROOT) $(addprefix -I, $(THIRD_INC))
# enable read over HTTP and FTP
DEFAULT_CXXFLAGS += -D_USE_KNETFILE

# 'make release' will: clean current build; build with -O2 flags;
# NOTE: don't use -j flag!
release: CXXFLAGS = -O2 -DNDEBUG $(DEFAULT_CXXFLAGS)
release: $(LIB) 

debug: CXXFLAGS = -Wall -ggdb -O0 $(DEFAULT_CXXFLAGS)
debug: $(LIB_DBG)

profile: CXXFLAGS = -Wall -pg -O0 $(DEFAULT_CXXFLAGS)
profile: $(LIB_DBG)

$(LIB): $(OBJ) 
	$(AR) rcs $@ $^
$(LIB_DBG): $(OBJ_DBG) 
	$(AR) rcs $@ $^

.SUFFIXES:
define BUILD_each
  TAR := $(1).o
  TAR_DBG := $(1)_dbg.o
  SRC := $(1).cpp
  -include  $(1).d
  $$(TAR) $$(TAR_DBG): $$(SRC) $$(THIRD_INC)
	$$(CXX) -MMD $$(CXXFLAGS) -c $$< -o $$@ $$(CXXFLAGS)
endef
$(foreach s, $(BASE), $(eval $(call BUILD_each, $(s))))

# %: %.cpp # remove implicit Makefile rule of converting ABC.cpp to ABC
# %.o: %.cpp %.h $(THIRD_INC)
# 	g++ -c $(CXXFLAGS) $<

clean:
	rm -rf $(LIB) $(LIB_DBG) *.o *.d
