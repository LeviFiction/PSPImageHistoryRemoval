# PSPImageHistoryRemoval
A short script to remove the history from a PSPImage file.  

## Purpose
Corel introduced saving editing history into the PSPImage file and JPG files.  Every edit you make is saved to the file.  This introduces several problems.
* If you ever passive-agressively wrote something into the file, ti's incldued
* It includes paths to files that were used in the creation of the image and so hints at your folder structure
* When adding patterns or textures it usually includes the actual image data for each into the history.
* If you use brushes or vectors this adds tons of text to the history
* Sometimes the history can be larger than the actual image.  With some people reporting history sizes of over 300MB.

## Issues with current script
Not widely tested - the new version has only been tested on a small examples.

## TODO:
* Export the XML data to a separate history file for preservation if required.


## Explanation of file format
PSPImage files are formatted with a fairly simple structure.  Blocks, sub-blocks, fields, and data chunks.
A block starts with "~BK" followed by an empty byte.  Then the next two bytes are the type of block.  And the next 4 are the length of the block.

The History of your edits are saved in XML format inside the Extended Data Block.  This block ID is 10.  It can hold Fields (with similar heading style) of 5 different types.  0 - Transparency Index, 1 - Grid data, 2 - Guides, 3 - EXIF, 4 - ?, 5 - XML data.  Inside the XML data are several information tags including an XML element <photoshop:History>.  It's between the opening and closing tags of this element that you find the history.  This script attempts to remove that tag and its child text leaving everything else alone.

Block Header: 4 Bytes - |~BK |
Block Type: 2 Bytes - 0A 00 - 10 - Extended Data Block
Block length: 4 Bytes - 

Field Header: 4 Bytes - |~FL |
Field Type: 2 Bytes - 05 00 - 5 - XML field
Field Length: 4 Bytes - 

## How the script works
When you want to remove the history from a file, first open it in PSP or save it if you're already done working on it.
Then run the script

The script will then grab the location of the current file and examine the format.  First it verifies that this is a valid PSPImage file.  Then it looks for Blocks by looking for the Block header "~BK" and then grabbing the type and length of the block.  If the block type is not type 10, then it jumps to the end of the block looking for the next one until block 10 is found.  
Once Block ten is found then a similar proceedure is done for the Fields.  Looking for the Field header "~FL" instead of the block header.  This likewise ignores any field taht is not of type 05.  If none are found, nothing is done the script just exits.
If the XML field is found, then the script goes though and tries to find the starting point of the <photoshop:History> tag and the length.
Finally the script creates a new file and begins to transfer data from the old file to the new file, updating the block and field length to make up for the lack of any history, and jumping over the history tag altogether.
The result is 2 imgaes, your original with the history and one without the history.
This same process can be performed to extract the history into a separate file for evaluation.
