include ../../Makefile.cfg

LZOPATH = ../../minilzo.106

CC	= $(HOSTCC)
CFLAGS	= $(HOSTCFLAGS) -DBAUD_RATE=$(SERIALSPEED) -DSERIALDEVICE="\"$(SERIALDEVICE)\"" -DHAVE_GETOPT -DB1500000 -DB500000
LDFLAGS = $(HOSTLDFLAGS)
INCLUDE	= -I$(LZOPATH) -I/usr/local/include

ifeq ($(WITH_BFD),1)
  CFLAGS	+= -DWITH_BFD -I$(BFDINCLUDE)
  LDFLAGS	+= -L$(BFDLIB)
else
  CFLAGS	+= 
  LDFLAGS	+= -lelf
endif

ifdef STANDALONE_BINARY
  LDFLAGS += -static
endif

DCTOOL	= dc-tool$(EXECUTABLEEXTENSION)

OBJECTS	= dc-tool.o minilzo.o syscalls.o unlink.o
LZOFILES = $(LZOPATH)/minilzo.c $(LZOPATH)/minilzo.h $(LZOPATH)/lzoconf.h

.c.o:
	$(CC) $(CFLAGS) $(INCLUDE) -o $@ -c $< 

all: $(DCTOOL)

$(DCTOOL): $(OBJECTS)
	$(CC) -o $@ $(OBJECTS) $(LDFLAGS)

minilzo.o: $(LZOFILES)
	$(CC) $(CFLAGS) $(INCLUDE) -o $@ -c $< 

.PHONY : install
install: $(DCTOOL) | $(TOOLINSTALLDIR)	
	cp $(DCTOOL) $(TOOLINSTALLDIR)
$(TOOLINSTALLDIR):
	-mkdir $(TOOLINSTALLDIR)

.PHONY : clean
clean:
	rm -f $(OBJECTS)

.PHONY : distclean
distclean: clean 
	rm -f $(DCTOOL)
