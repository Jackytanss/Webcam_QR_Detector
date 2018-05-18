# QR and Bar code detector

This project is forked from [zbar](https://github.com/NaturalHistoryMuseum/pyzbar) library, I added few modification, so webcam can be used as an image reader to detect QR and Bar codes.

[![Python Versions](https://img.shields.io/badge/python-2.7%2C%203.4%2C%203.5%2C%203.6-blue.svg)](https://github.com/NaturalHistoryMuseum/pyzbar)
[![PyPI version](https://badge.fury.io/py/pyzbar.svg)](https://pypi.python.org/pypi/pyzbar/)
[![Travis status](https://travis-ci.org/NaturalHistoryMuseum/pyzbar.svg?branch=master)](https://travis-ci.org/NaturalHistoryMuseum/pyzbar)
[![Coverage Status](https://coveralls.io/repos/github/NaturalHistoryMuseum/pyzbar/badge.svg?branch=master)](https://coveralls.io/github/NaturalHistoryMuseum/pyzbar?branch=master)

* Pure python
* Works with PIL / Pillow images, OpenCV / numpy `ndarray`s, and raw bytes
* Decodes locations of barcodes
* No dependencies, other than the zbar library itself
* Tested on Python 2.7, and Python 3.4 to 3.6

## Installation

Mac OS X:

```
brew install zbar
```

Linux:

```
sudo apt-get install libzbar0
```

Install this Python wrapper; use the second form to install dependencies of
the command-line scripts:

```
pip install pyzbar
pip install pyzbar[scripts]
```

## Example usage

Go to see the Jupyter Notebook for more details.

- [QR_Bar_Code_Detector_Basic]()
- [QR_Bar_Code_Detector_Webcam]()

The `decode` function accepts instances of `PIL.Image`.

```Python
from pyzbar.pyzbar import decode
from PIL import Image
decode(Image.open('pyzbar/tests/code128.png'))
```
Output:
```
[
    Decoded(
        data=b'Foramenifera', type='CODE128',
        rect=Rect(left=37, top=550, width=324, height=76),
        polygon=[
            Point(x=37, y=551), Point(x=37, y=625), Point(x=361, y=626),
            Point(x=361, y=550)
        ]
    )
    Decoded(
        data=b'Rana temporaria', type='CODE128',
        rect=Rect(left=4, top=0, width=390, height=76),
        polygon=[
            Point(x=4, y=1), Point(x=4, y=75), Point(x=394, y=76),
            Point(x=394, y=0)
        ]
    )
]
```

It also accepts instances of `numpy.ndarray`, which might come from loading
images using [OpenCV](http://opencv.org/).

```python
import cv2
decode(cv2.imread('pyzbar/tests/code128.png'))
```
Output:
``` 
[
    Decoded(
        data=b'Foramenifera', type='CODE128',
        rect=Rect(left=37, top=550, width=324, height=76),
        polygon=[
            Point(x=37, y=551), Point(x=37, y=625), Point(x=361, y=626),
            Point(x=361, y=550)
        ]
    )
    Decoded(
        data=b'Rana temporaria', type='CODE128',
        rect=Rect(left=4, top=0, width=390, height=76),
        polygon=[
            Point(x=4, y=1), Point(x=4, y=75), Point(x=394, y=76),
            Point(x=394, y=0)
        ]
    )
]
```

You can also provide a tuple `(pixels, width, height)`, where the image data
is eight bits-per-pixel.
 
```python
image = cv2.imread('pyzbar/tests/code128.png')
height, width = image.shape[:2]

# 8 bpp by considering just the blue channel
decode((image[:, :, 0].astype('uint8').tobytes(), width, height))
```
Output:
``` 
[
    Decoded(
        data=b'Foramenifera', type='CODE128',
        rect=Rect(left=37, top=550, width=324, height=76),
        polygon=[
            Point(x=37, y=551), Point(x=37, y=625), Point(x=361, y=626),
            Point(x=361, y=550)
        ]
    )
    Decoded(
        data=b'Rana temporaria', type='CODE128',
        rect=Rect(left=4, top=0, width=390, height=76),
        polygon=[
            Point(x=4, y=1), Point(x=4, y=75), Point(x=394, y=76),
            Point(x=394, y=0)
        ]
    )
]
```

```Python
# 8 bpp by converting image to greyscale
grey = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
decode((grey.tobytes(), width, height))
```
Output:
``` 
[
    Decoded(
        data=b'Foramenifera', type='CODE128',
        rect=Rect(left=37, top=550, width=324, height=76),
        polygon=[
            Point(x=37, y=551), Point(x=37, y=625), Point(x=361, y=626),
            Point(x=361, y=550)
        ]
    )
    Decoded(
        data=b'Rana temporaria', type='CODE128',
        rect=Rect(left=4, top=0, width=390, height=76),
        polygon=[
            Point(x=4, y=1), Point(x=4, y=75), Point(x=394, y=76),
            Point(x=394, y=0)
        ]
    )
]
```

```Python

# If you don't provide 8 bpp
decode((image.tobytes(), width, height))
```
Output:
``` 

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Users/lawh/projects/pyzbar/pyzbar/pyzbar.py", line 102, in decode
    raise PyZbarError('Unsupported bits-per-pixel [{0}]'.format(bpp))
pyzbar.pyzbar_error.PyZbarError: Unsupported bits-per-pixel [24]
```

The default behaviour is to decode all symbol types. You can look for just your
symbol types

```python
from pyzbar.pyzbar import ZBarSymbol
# Look for just qrcode
decode(Image.open('pyzbar/tests/qrcode.png'), symbols=[ZBarSymbol.QRCODE])
```
Output:
``` 
[
    Decoded(
        data=b'Thalassiodracon', type='QRCODE',
        rect=Rect(left=27, top=27, width=145, height=145),
        polygon=[
            Point(x=27, y=27), Point(x=27, y=172), Point(x=172, y=172),
            Point(x=172, y=27)
        ]
    )
]
```
```Python

# If we look for just code128, the qrcodes in the image will not be detected
decode(Image.open('pyzbar/tests/qrcode.png'), symbols=[ZBarSymbol.CODE128])
[]
```

## Bounding boxes and polygons

The blue and pink boxes show `rect` and `polygon`, respectively, for barcodes in
`pyzbar/tests/qrcode.png`
(see [bounding_box_and_polygon.py](https://github.com/NaturalHistoryMuseum/pyzbar/blob/master/bounding_box_and_polygon.py)).

![Two barcodes with bounding boxes and polygons](https://github.com/NaturalHistoryMuseum/pyzbar/raw/master/bounding_box_and_polygon.png)

## License

`pyzbar` is distributed under the MIT license (see `LICENCE.txt`).
The `zbar` shared library is distributed under the GNU Lesser General Public
License, version 2.1 (see `zbar-LICENCE.txt`).
