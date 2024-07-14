PYODIDE_ROOT=$(abspath ../..)
include ../../Makefile.envs

TARBALL=qt-everywhere-src-5.15.2.tar.xz
QT_TARBALL_URL=https://download.qt.io/official_releases/qt/5.15/5.15.2/single/qt-everywhere-src-5.15.2.tar.xz

all: qt5/.complete
linkargs: qt5/linkargs/Makefile

$(TARBALL):
	wget -q -O $@ $(QT_TARBALL_URL)

qt5/.extracted: $(TARBALL) $(wildcard patches/*.patch)
	mkdir -p qt5/src
	tar -xJf $(TARBALL) -C qt5/src --strip-components=1
	cd qt5/src && cat ../../patches/*.patch | patch -p1 --verbose
	touch qt5/.extracted

qt5/.complete: ../../Makefile.envs qt5/.extracted
	source $(PYODIDE_ROOT)/pyodide_env.sh && cd qt5/src && ./configure -xplatform wasm-emscripten -nomake examples -prefix $(PYODIDE_ROOT)/qt/qt5/qt5/dist/ -no-feature-thread -opensource -confirm-license
	source $(PYODIDE_ROOT)/pyodide_env.sh && cd qt5/src && make -j12 module-qtbase module-qtdeclarative module-qtsvg
	source $(PYODIDE_ROOT)/pyodide_env.sh && cd qt5/src && make module-qtbase-install_subtargets module-qtdeclarative-install_subtargets module-qtsvg-install_subtargets
	touch qt5/.complete
	
qt5/linkargs/Makefile: qt5/.complete
	mkdir -p qt5/linkargs
	cp pyodide.pro qt5/linkargs/pyodide.pro
	source $(PYODIDE_ROOT)/pyodide_env.sh && cd qt5/linkargs && $(PYODIDE_ROOT)/qt/qt5/qt5/dist/bin/qmake pyodide.pro

clean:
	rm -rf qt5
