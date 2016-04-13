#!/bin/bash

##
## mv-screenshots
## --------------
## Moves screenshots from the current directory to a destination directory.
## Removes colons from the filename for Windows compatibility.
##
## Usage: mv-screenshots <DESTINATION>
##

if [ "$1" == "" ] || [ "$1" == "--help" ] || [ "$1" == "-h" ] ; then
    grep -E '^## ?' "$0" | sed -E 's/^## ?//g'
    exit
fi

DEST_DIR="$1"

for f in *png ; do 
    # Make xfce4-screenshooter output sort chronologically
    if [ "$(echo $f | grep -Ei ':[0-9]{2} (AM|PM)')" != "" ]; then
        NEW_NAME=$(echo "$f" | tr ':' '-' | tr '.' ' ' | awk -F ' ' '{print $1 " " $2 " " $3 " " $4 " " $6 " " $5 "." $7}')
        NEW_NAME=$(echo $NEW_NAME | sed 's/AM 12/AM 00/g' | sed 's/PM 12/PM 00/g')
        mv -v "$f" "$DEST_DIR/$NEW_NAME"
    else
        # Generic handler for Kali default screenshot names
        mv -v "$f" "$DEST_DIR/$(echo $f | tr ':' '-')"
    fi
done
