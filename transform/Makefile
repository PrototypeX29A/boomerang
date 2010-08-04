######################################################
# File: Makefile
# Desc: Makefile for the transformation (transform) directory
#
######################################################

# $Revision: 1.5 $
# 18 Apr 04 - Trent: Created.

top_srcdir = ..

LIB_OBJS = transform.o
LIB=$(top_srcdir)/lib/transform.so

CC = gcc
CXX = g++
CFLAGS = -g -O2 -Wall
CXXFLAGS = -g -O2 -Wall
CPPFLAGS = -I$(top_srcdir)/include 
LDFLAGS = 

all: #$(LIB) testTransform
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

testTransform: testTransform.o TransformTest.o $(LIB)
	$(CC) -o $@ testTransform.o TransformTest.o -ltransform -lcppunit $(RUNPATH) -L$(top_srcdir)/lib

$(LIB): $(LIB_OBJS)
	$(CC) -o $@ -shared $(LIB_OBJS) $(RUNPATH) -L$(top_srcdir)/lib

TransformTest.o: TransformTest.h
transform.o: ../include/transformer.h

# Make these objects with -fPIC
$(LIB_OBJS): %.o : %.cpp
	$(CC) -c -fPIC -I$(top_srcdir)/include $(EXTRAS) -o $@ $<

# Others without -fPIC
.cpp.o:
	$(CC) -c -I$(top_srcdir)/include -o $@ $<

clean:
	rm -f testTransform *.o $(LIB)

test: $(LIB) testTransform
	./testTransform
