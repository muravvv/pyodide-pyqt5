<div align="center">
  <a href="https://github.com/pyodide/pyodide">
  <img src="./docs/_static/img/pyodide-logo-readme.png" alt="Pyodide">
  </a>
</div>

Pyodide with PyQt5

This is not a standard pyodide package: PyQt5 is built statically into main pyodide module. For now Qt cannot be built as shared library (see [QTBUG-63925](https://bugreports.qt.io/browse/QTBUG-63925)).

## How to build (using docker)

1. Install Docker
1. `./run_docker`
1. `pip install sip`
1. `pip install PyQt-builder`
1. `make`

## Example

`example.py` - PyQt application:

```python
# -*- coding: utf-8 -*-

import sys
from PyQt5.QtWidgets import QWidget, QPushButton, QApplication
from PyQt5.QtCore import QCoreApplication

class Example(QWidget):
    def __init__(self):
        super().__init__()
        self.initUI()

    def initUI(self):
        qbtn = QPushButton('Quit', self)
        qbtn.clicked.connect(QCoreApplication.instance().quit)
        qbtn.resize(qbtn.sizeHint())
        qbtn.move(50, 50)

        self.setWindowTitle('Quit button')
        self.show()

if __name__ == '__main__':
    app = QApplication(sys.argv)
    ex = Example()
    #do not call "app.exec_()" here
```

HTML page:

```html
<!doctype html>
<html>
  <head>
  </head>
  <body>
    <!-- pyqt application will be shown on this canvas -->
    <canvas id="qtcanvas" oncontextmenu="event.preventDefault()" contenteditable="true"></canvas>
	
    <script type="text/javascript">
      async function main(){
        let indexURL = "./";
        var canvas = document.querySelector('#qtcanvas');

        const { loadPyodide } = await import(indexURL + "pyodide.mjs");
        // to facilitate debugging
        globalThis.loadPyodide = loadPyodide;

        globalThis.pyodide = await loadPyodide({
          canvasElements : [canvas],
        });

        // load our pyqt application
        pyodide.runPython(await (await fetch("./example.py")).text());

        // QApplication.exec() cannot be called from python code, so call it from JS here
        pyodide.execQApplication(); 
      }
      main();
    </script>
  </body>
</html>
```
