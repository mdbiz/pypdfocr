SlimPyPDFOCR Fork - Simple Tesseract-OCR based PDF filing
=========================================

Slimmed down fork of PyPDFOCR.
Removing unnecessary options and fixing usual bugs that were left since project abandonment.
Also updating necessary parts to enable using newer versions of dependencies: Tesseract OCR software, GhostScript, ImageMagick and Poppler.

Evernote support is removed. Slim aims to be simple PDF OCR management and thus removing Evernote and focusing on folder/file management.

This fork is focused on Windows usage. No guarantee for Mac OS and Linux.

SlimPyPDFOCR works with Python 2. No support for Python 3.

This program will help manage your scanned PDFs by doing the following:

-  Take a scanned PDF file and run OCR on it (using the Tesseract OCR
   software from Google), generating a searchable PDF
-  Optionally, watch a folder for incoming scanned PDFs and
   automatically run OCR on them
-  Optionally, file the scanned PDFs into directories based on simple
   keyword matching that you specify
-  Email status when it files your PDF

More links:

-  `Blog @ virantha.com <http://virantha.com/category/pypdfocr.html>`__
-  `Documentation @ gitpages <http://virantha.github.com/pypdfocr/html>`__
-  `Source @ github <https://www.github.com/virantha/pypdfocr>`__

Usage:
######

Single conversion:
~~~~~~~~~~~~~~~~~~

::

    pypdfocr filename.pdf

    --> filename_ocr.pdf will be generated

If you have a language pack installed, then you can specify it with the
``-l`` option:

::

    pypdfocr -l spa filename.pdf

Folder monitoring:
~~~~~~~~~~~~~~~~~~

::

    pypdfocr -w watch_directory

    --> Every time a pdf file is added to `watch_directory` it will be OCR'ed

Automatic filing:
~~~~~~~~~~~~~~~~~

To automatically move the OCR'ed pdf to a directory based on a keyword,
use the -f option and specify a configuration file (described below):

::

    pypdfocr filename.pdf -f -c config.yaml

You can also do this in folder monitoring mode:

::

    pypdfocr -w watch_directory -f -c config.yaml

Filing based on filename match:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If no keywords match the contents of the filename, you can optionally
allow it to fallback to trying to find keyword matches with the PDF
filename using the -n option. For example, you may have receipts always
named as ``receipt_2013_12_2.pdf`` by your scanner, and you want to move
this to a folder called 'receipts'. Assuming you have a keyword
``receipt`` matching to folder ``receipts`` in your configuration file
as described below, you can run the following and have this filed even
if the content of the pdf does not contain the text 'receipt':

::

    pypdfocr filename.pdf -f -c config.yaml -n

Configuration file for automatic PDF filing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The config.yaml file above is a simple folder to keyword matching text
file. It determines where your OCR'ed PDFs (and optionally, the original
scanned PDF) are placed after processing. An example is given below:

::

    target_folder: "docs/filed"
    default_folder: "docs/filed/manual_sort"
    original_move_folder: "docs/originals"

    folders:
        finances:
            - american express
            - chase card
            - internal revenue service
        travel:
            - boarding pass
            - airlines
            - expedia
            - orbitz
        receipts:
            - receipt
            
Windows path format example:

::

    target_folder: "C:/OCR/docs/filed"
    default_folder: "C:/OCR/docs/filed/manual_sort"
    original_move_folder: "C:/OCR/docs/originals"

The ``target_folder`` is the root of your filing cabinet. Any PDF moving
will happen in sub-directories under this directory.

The ``folders`` section defines your filing directories and the keywords
associated with them. In this example, we have three filing directories
(finances, travl, receipts), and some associated keywords for each
filing directory. For example, if your OCR'ed PDF contains the phrase
"american express" (in any upper/lower case), it will be filed into
``docs/filed/finances``

The ``default_folder`` is where the OCR'ed PDF is moved to if there is
no keyword match.

The ``original_move_folder`` is optional (you can comment it out with
``#`` in front of that line), but if specified, the original scanned PDF
is moved into this directory after OCR is done. Otherwise, if this field
is not present or commented out, your original PDF will stay where it
was found.

If there is any naming conflict during filing, the program will add an
underscore followed by a number to each filename, in order to avoid
overwriting files that may already be present.

Auto email
~~~~~~~~~~

You can have PyPDFOCR email you everytime it converts a file and files
it. You need to first specify the following lines in the configuration
file and then use the ``-m`` option when invoking ``pypdfocr``:

::

    mail_smtp_server: "smtp.gmail.com:587"
    mail_smtp_login: "virantha@gmail.com"
    mail_smtp_password: "PASSWORD"
    mail_from_addr: "virantha@gmail.com"
    mail_to_list: 
        - "virantha@gmail.com"
        - "person2@gmail.com"


Advanced options
################

Fine-tuning Tesseract/Ghostscript/others
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can specify Tesseract and Ghostscript executable locations manually, as
well as the number of concurrent processes allowed during preprocessing and
tesseract.  Use the following in your configuration file:

::

    tesseract:
        binary: "/usr/bin/tesseract"
        threads: 8

    ghostscript:
        binary: "/usr/local/bin/gs"

    preprocess:
        threads: 8

On Windows use following format:

::

    tesseract:
        binary: "C:/Program Files/Tesseract-OCR/tesseract.exe"
        threads: 8

    ghostscript:
        binary: "C:/Program Files/gs/gs9.26/bin/gswin64c.exe"

    preprocess:
        threads: 8

Handling disk time-outs
~~~~~~~~~~~~~~~~~~~~~~~
If you need to increase the time interval (default 3 seconds) between new
document scans when pypdfocr is watching a directory, you can specify the following
option in the configuration file:

::
    
    watch:
        scan_interval: 6

Installation
############

SlimPyPDFOCR works with Python 2. No support for Python 3.

Using pip
~~~~~~~~~

You can just run:

::

    pip install git+git://github.com/mdbiz/pypdfocr.git

Or:

::

    pip install git+https://github.com/mdbiz/pypdfocr.git

Manual install
~~~~~~~~~~~~~~

Clone the source directly from github (you need to have git installed):

::

    git clone https://github.com/mdbiz/pypdfocr.git

Then, install the following third-party python libraries:

-  Pillow (Python Imaging Library) https://pillow.readthedocs.org/en/3.1.x/
-  ReportLab (PDF generation library)
   http://www.reportlab.com/opensource/
-  Watchdog (Cross-platform fhlesystem events monitoring)
   https://pypi.python.org/pypi/watchdog
-  PyPDF2 (Pure python pdf library)

These can all be installed via pip:

::

    pip install Pillow
    pip install reportlab
    pip install watchdog
    pip install pypdf2


You will also need to install the external dependencies listed below.

External Dependencies
~~~~~~~~~~~~~~~~~~~~~

PyPDFOCR relies on the following (free) programs being installed and in
the path:

-  Tesseract OCR software https://code.google.com/p/tesseract-ocr/
-  GhostScript http://www.ghostscript.com/
-  ImageMagick http://www.imagemagick.org/
-  Poppler http://poppler.freedesktop.org/  (`Windows <http://sourceforge.net/projects/poppler-win32/>`__)

Poppler is only required if you want pypdfocr to figure out the original PDF resolution
automatically; just make sure you have ``pdfimages`` in your path.   Note that the 
`xpdf <http://www.foolabs.com/xpdf/download.html>`__ provided ``pdfimages`` does not work for this, 
because it does not support the ``-list`` option to list the table of images in a PDF file.

On Mac OS X, you can install these using homebrew:

::

    brew install tesseract
    brew install ghostscript
    brew install poppler
    brew install imagemagick

On Windows, please use the installers provided on their download pages.

\*\* Important \*\* Tesseract version 3.02.02 or newer required
(apparently 3.02.01-6 and possibly others do not work due to a hocr
output format change that I'm not planning to address). On Ubuntu, you
may need to compile and install it manually by following `these
instructions <http://miphol.com/muse/2013/05/install-tesseract-ocr-on-ubunt.html>`__

Also note that if you want Tesseract to recognize rotated documents (upside down, or rotated 90 degrees)
then you need to find your tessdata directory and do the following:

::

    cd /usr/local/share/tessdata 
    cp eng.traineddata osd.traineddata 

``osd`` stands for Orientation and Script Detection, so you need to copy the .traineddata
for whatever language you want to scan in as ``osd.traineddata``.  If you don't do this step, 
then any landscape document will produce garbage

Disclaimer
##########

The software is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.

