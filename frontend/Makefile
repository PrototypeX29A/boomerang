######################################################
# File: Makefile
# Desc: Makefile for the frontend directory
#       Makes and tests the frontend.so library, and
#         the machine dependent decoders
#
# Prerequisite libraries: db, util, BinaryFile
#
######################################################

# $Revision: 1.8 $
# 08 Apr 02 - Mike: Created.
# 10 Apr 02 - Mike: interface -> include

top_srcdir = ..

FRONTEND_LIB = $(top_srcdir)/lib/libfrontend.so
TEST_OBJS = FrontSparcTest.o FrontPentTest.o # FrontendTest.o 
LIB_OBJS  = frontend.o njmcDecoder.o

MACHS     = sparc pentium st20 ppc mips arm hppa
MACH_LIBS = $(patsubst %, $(top_srcdir)/lib/libfront%.so, $(MACHS))
MACH_OBJS = $(patsubst %, %decoder.o, $(MACHS))
MACH_SRC  = $(patsubst %, %decoder.cpp, $(MACHS))
MACH_FE_SRC=$(patsubst %, %frontend.cpp, $(MACHS))
MACH_FE_OBJ=$(patsubst %, %frontend.o, $(MACHS))

CC = gcc
CXX = g++
CFLAGS = -g -O2 -Wall
CXXFLAGS = -g -O2 -Wall
CPPFLAGS = -I$(top_srcdir)/include 
LDFLAGS = 

GENFILES = $(MACH_SRC)

all: #testFront
	# Only "make test" is supported

# HOST_GNU_LD is true if the linker on this host machine is GNU
HOST_GNU_LD = yes

BOOMDIR="$(PWD)/.."
BOOMSET=-DBOOMDIR=\"$(BOOMDIR)\"
LIBDIR=$(BOOMDIR)/lib
ifeq ($(HOST_GNU_LD), yes)
RUNPATH=-Wl,-rpath -Wl,$(LIBDIR)
else # Assume Solaris
RUNPATH=-R$(LIBDIR)
endif

testFront: testFront.o $(FRONTEND_LIB) $(TEST_OBJS) $(MACH_LIBS) \
        ../include/frontend.h
	$(CC) -o $@ testFront.o $(TEST_OBJS) -lcppunit -lfrontend -ldb \
		-lBinaryFile -lutil $(RUNPATH) -L$(top_srcdir)/lib

$(FRONTEND_LIB): $(LIB_OBJS)
	$(CC) -shared -o $@ $(LIB_OBJS) $(RUNPATH) -L$(top_srcdir)/lib

# A pattern for the machine dependent libraries
$(MACH_LIBS): ../lib/libfront%.so : %decoder.o %frontend.o
	$(CC) -shared -o $@ $^ $(RUNPATH) -L$(top_srcdir)/lib

# Dependencies
frontend.o: frontend.cpp ../include/frontend.h ../include/decoder.h
FrontendTest.o: FrontendTest.h
FrontSparcTest.o: FrontSparcTest.h
FrontPentTest.o:  FrontPentTest.h
njmcDecoder.o: njmcDecoder.cpp ../include/decoder.h

# Extras
FrontSparcTest.o: EXTRAS = -I.  # To include frontend.h
FrontPentTest.o:  EXTRAS = -I.  # To include frontend.h
frontend.o:       EXTRAS = $(BOOMSET)

# The .cpp's depend on the .m's
$(MACH_SRC): %decoder.cpp : machine/%/core.spec \
                                   machine/%/synth.spec \
                                   machine/%/dis.spec \
                                   machine/%/decoder.m
	./mltk.sh -o $@ $^ 2> mltk.err

# The library objs require -fPIC
$(LIB_OBJS):
	$(CC) -c -o $@ -I$(top_srcdir)/include -I. -fPIC $(EXTRAS) $<

# Also the machine dependent decoders
$(MACH_OBJS) $(MACH_FE_OBJ): %.o : %.cpp ../include/decoder.h
	$(CC) -c -o $@ -I$(top_srcdir)/include -I. -fPIC $<

.cpp.o:
	$(CC) -c -o $@ -I$(top_srcdir)/include $(BOOMSET) $(EXTRAS) $<

clean:
	rm -f testFront $(FRONTEND_LIB) $(MACH_OBJS) $(MACH_FE_OBJS) $(MACH_LIBS)
	find . -name '*.o' -exec rm {} \;

# Clean up generated files
cleangen:
	rm -f $(GENFILES)

# Remote: for when making remotely from bison++/flex++ facilities, or just
# can't be bothered installing these.
remote:
	touch $(GENFILES)

test: testFront
	./testFront
