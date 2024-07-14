PYODIDE_ROOT=$(abspath ../..)
include ../../Makefile.envs

TARBALL=PyQt5-5.15.10.tar.gz
PYQT5_TARBALL_URL=https://files.pythonhosted.org/packages/4d/5d/b8b6e26956ec113ad3f566e02abd12ac3a56b103fcc7e0735e27ee4a1df3/PyQt5-5.15.10.tar.gz

all: PyQt5/.complete

$(TARBALL):
	wget -q -O $@ $(PYQT5_TARBALL_URL)

PyQt5/.extracted: $(TARBALL) $(wildcard patches/*.patch)
	mkdir -p PyQt5/src
	tar -xzf $(TARBALL) -C PyQt5/src --strip-components=1
	cd PyQt5/src && cat ../../patches/*.patch | patch -p1 --verbose
	touch PyQt5/.extracted

PyQt5/src/pyproject.toml.patched:
	echo "" >> PyQt5/src/pyproject.toml
	echo "[tool.sip.project]" >> PyQt5/src/pyproject.toml
	echo "py-include-dir = \"$(PYTHONINCLUDE)\"" >> PyQt5/src/pyproject.toml
	echo "py-platform = \"emscripten\"" >> PyQt5/src/pyproject.toml
	echo "py-major-version = $(PYMAJOR)" >> PyQt5/src/pyproject.toml
	echo "py-minor-version = $(PYMINOR)" >> PyQt5/src/pyproject.toml
	echo "py-pylib-dir = \"$(CPYTHONINSTALL)/lib\"" >> PyQt5/src/pyproject.toml
	echo "target-dir = \"$(PYODIDE_ROOT)/qt/PyQt5/PyQt5/dist\"" >> PyQt5/src/pyproject.toml
	echo "tools = false" >> PyQt5/src/pyproject.toml
	#echo "verbose = true" >> PyQt5/src/pyproject.toml
	echo "enable = [\"Qt\", \"QtCore\", \"QtGui\", \"QtWidgets\"]" >> PyQt5/src/pyproject.toml
	echo "distinfo = false" >> PyQt5/src/pyproject.toml
	echo "" >> PyQt5/src/pyproject.toml
	echo "[tool.sip.bindings.Qt]" >> PyQt5/src/pyproject.toml
	echo "static = true" >> PyQt5/src/pyproject.toml
	echo "" >> PyQt5/src/pyproject.toml
	echo "[tool.sip.bindings.QtCore]" >> PyQt5/src/pyproject.toml
	echo "disabled-features = [\"PyQt_Desktop_OpenGL\"]" >> PyQt5/src/pyproject.toml
	echo "static = true" >> PyQt5/src/pyproject.toml
	echo "" >> PyQt5/src/pyproject.toml
	echo "[tool.sip.bindings.QtGui]" >> PyQt5/src/pyproject.toml
	echo "static = true" >> PyQt5/src/pyproject.toml
	echo "" >> PyQt5/src/pyproject.toml
	echo "[tool.sip.bindings.QtWidgets]" >> PyQt5/src/pyproject.toml
	echo "static = true" >> PyQt5/src/pyproject.toml

	touch PyQt5/src/pyproject.toml.patched

# ../../Makefile.envs
PyQt5/.complete: PyQt5/.extracted PyQt5/src/pyproject.toml.patched
	source $(PYODIDE_ROOT)/pyodide_env.sh && cd PyQt5/src && sip-build --no-make --qmake ../../../qt5/qt5/dist/bin/qmake --confirm-license
	source $(PYODIDE_ROOT)/pyodide_env.sh && cd PyQt5/src/build && make -j12
	source $(PYODIDE_ROOT)/pyodide_env.sh && cd PyQt5/src/build && make install
	touch PyQt5/.complete

clean:
	rm -rf PyQt5
