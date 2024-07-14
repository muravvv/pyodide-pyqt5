PYODIDE_ROOT=$(abspath ../..)
include ../../Makefile.envs

CC=emcc
CXX=em++

TARBALL=PyQt5_sip-12.13.0.tar.gz
PYQT5_SIP_TARBALL_URL=https://files.pythonhosted.org/packages/ee/81/fce2a475aa56c1f49707d9306b930695b6ff078c2242c9f2fd72a3214e1f/PyQt5_sip-12.13.0.tar.gz

PYQT5_SIP_CFLAGS= $(CFLAGS_BASE) -I$(PYTHONINCLUDE)

all: PyQt5_sip/dist/libsip.a

$(TARBALL):
	wget -q -O $@ $(PYQT5_SIP_TARBALL_URL)

PyQt5_sip/.extracted: $(TARBALL) $(wildcard patches/*.patch)
	mkdir -p PyQt5_sip/src
	tar -xzf $(TARBALL) -C PyQt5_sip/src --strip-components=1
	cd PyQt5_sip/src && cat ../../patches/*.patch | patch -p1 --verbose
	touch PyQt5_sip/.extracted

PYQT5_SIP_OBJS= \
	PyQt5_sip/src/apiversions.o \
	PyQt5_sip/src/objmap.o \
	PyQt5_sip/src/siplib.o \
	PyQt5_sip/src/descriptors.o \
	PyQt5_sip/src/qtlib.o \
	PyQt5_sip/src/threads.o \
	PyQt5_sip/src/int_convertors.o \
	PyQt5_sip/src/sip_array.o \
	PyQt5_sip/src/voidptr.o

PyQt5_sip/dist/libsip.a: ../../Makefile.envs PyQt5_sip/.extracted $(PYQT5_SIP_OBJS)
	mkdir -p PyQt5_sip/dist
	emar rcs PyQt5_sip/dist/libsip.a $(PYQT5_SIP_OBJS)

%.o: %.c $(CPYTHONLIB)
	$(CC) -o $@ -c $< $(PYQT5_SIP_CFLAGS) -Isrc/core/

clean:
	rm -rf PyQt5_sip
