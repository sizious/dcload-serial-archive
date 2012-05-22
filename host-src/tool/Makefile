include ../../Makefile.cfg

LZOPATH = ../../minilzo.106

CC	= $(HOSTCC)
CFLAGS	= $(HOSTCFLAGS) -DBAUD_RATE=$(SERIALSPEED) -DSERIALDEVICE="\"$(SERIALDEVICE)\"" -DHAVE_GETOPT
INCLUDE	= -I$(LZOPATH) -I$(BFDINCLUDE)

ifdef WITH_BFD
	CFLAGS	+= -DWITH_BFD
	LDFLAGS	= $(HOSTLDFLAGS) $(BFDLIB)
else
	LDFLAGS	= $(HOSTLDFLAGS) -lelf
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
install: $(DCTOOL)
	-mkdir $(TOOLINSTALLDIR)
	cp $(DCTOOL) $(TOOLINSTALLDIR)

.PHONY : clean
clean:
	rm -f $(OBJECTS)

.PHONY : distclean
distclean: clean 
	rm -f $(DCTOOL)
