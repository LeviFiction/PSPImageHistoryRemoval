# PSPImageHistoryRemoval
A short script to remove the history from a PSPImage file.  

# Purpose
Corel introduced saving editing history into the PSPImage file and JPG files.  Every edit you make is saved to the file.  This introduces several problems.
* If you ever passive-agressively wrote something into the file, ti's incldued
* It includes paths to files that were used in the creation of the image and so hints at your folder structure
* When adding patterns or textures it usually includes the actual image data for each into the history.
* If you use brushes or vectors this adds tons of text to the history
* Sometimes the history can be larger than the actual image.  With some people reporting history sizes of over 300MB.

## Issues with current script
History and EXIF data are both stored in the Extended Data block (10) with the field array (05), currently the script fully removes this field array which destroys EXIF along with the history.  

## TODO:
* Remove only the <photoshop:History> element and child data.  Leaving the rest of file array (05) intact.
* Export the XML data to a separate history file for preservation if required.
